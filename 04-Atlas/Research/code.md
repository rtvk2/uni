import torch
import torch.nn as nn
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

torch.manual_seed(0)
np.random.seed(0)
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

# matrix dimensions and problem setup
m, n = 250, 500
sparsity = 0.1 
noise_std = 0.01
K = 16 

# random measurement matrix A, rough column normalization
A_np = np.random.randn(m, n) / np.sqrt(m)
A = torch.tensor(A_np, dtype=torch.float32, device=device)

# lipschitz constant for the step size
L = torch.linalg.eigvalsh(A.T @ A).max().item() 
alpha_ista = 1.0 / L

def get_batch(batch_size):
    # build sparse signals and noisy measurements
    x_true = torch.zeros(batch_size, n, device=device)
    for i in range(batch_size):
        support = np.random.choice(n, size=int(sparsity * n), replace=False)
        vals = np.random.randn(len(support)) * 2.0
        x_true[i, support] = torch.tensor(vals, dtype=torch.float32, device=device)
    
    noise = torch.randn(batch_size, m, device=device) * noise_std
    b = x_true @ A.T + noise
    return x_true, b

def soft_thresh(x, theta):
    return torch.sign(x) * torch.clamp(x.abs() - theta, min=0.0)

def run_ista(b, x_true, lam, iters):
    x = torch.zeros(b.shape[0], n, device=device)
    theta = lam * alpha_ista
    trace = []
    
    for k in range(iters):
        grad = (x @ A.T - b) @ A 
        x = soft_thresh(x - alpha_ista * grad, theta)
        
        err = ((x - x_true) ** 2).sum(dim=1)
        denom = (x_true ** 2).sum(dim=1) + 1e-12
        nmse = (err / denom).mean().item()
        trace.append(10 * np.log10(nmse + 1e-12))
        
    return trace

class LISTA(nn.Module):
    def __init__(self, A, K):
        super().__init__()
        # initialize based on A
        W1_start = alpha_ista * A.T
        W2_start = torch.eye(n, device=A.device) - alpha_ista * (A.T @ A) 
        
        # untied parameters for every layer
        self.W1 = nn.ParameterList([nn.Parameter(W1_start.clone()) for _ in range(K)])
        self.W2 = nn.ParameterList([nn.Parameter(W2_start.clone()) for _ in range(K)])
        self.theta = nn.ParameterList([nn.Parameter(torch.tensor(0.1)) for _ in range(K)])
        self.K = K

    def forward(self, b, get_trace=False, x_true=None):
        x = torch.zeros(b.shape[0], self.W1[0].shape[0], device=b.device)
        trace = []
        
        for k in range(self.K):
            x = soft_thresh(b @ self.W1[k].T + x @ self.W2[k].T, self.theta[k])
            
            if get_trace:
                err = ((x - x_true) ** 2).sum(dim=1)
                denom = (x_true ** 2).sum(dim=1) + 1e-12
                nmse = (err / denom).mean().item()
                trace.append(10 * np.log10(nmse + 1e-12))
                
        if get_trace:
            return x, trace
        return x

# training
lista = LISTA(A, K).to(device)
opt = torch.optim.Adam(lista.parameters(), lr=1e-3)

epochs = 2000
batch_size = 64

print("training lista...")
for epoch in range(epochs):
    x_true, b = get_batch(batch_size)
    x_pred = lista(b)
    loss = ((x_pred - x_true) ** 2).sum(dim=1).mean()
    
    opt.zero_grad()
    loss.backward()
    opt.step()
    
    if (epoch + 1) % 400 == 0:
        print(f"epoch {epoch+1}/{epochs} | loss: {loss.item():.4f}")

# testing
torch.manual_seed(123) 
x_test, b_test = get_batch(500)

ista_iters = 800
lambdas = [0.025, 0.05, 0.1]
traces = {lam: run_ista(b_test, x_test, lam, ista_iters) for lam in lambdas}

with torch.no_grad():
    _, lista_trace = lista(b_test, get_trace=True, x_true=x_test)

# plotting
plt.figure(figsize=(7, 5))
for lam, trace in traces.items():
    plt.plot(trace, label=f"ista (lam={lam})")
plt.plot(range(K), lista_trace, "o-", color="tab:blue", label="lista", linewidth=2, markersize=5)

plt.xlabel("layers / iterations")
plt.ylabel("nmse (db)")
plt.title(f"lista (k={K}) vs ista")
plt.legend()
plt.grid(alpha=0.3)
plt.tight_layout()
plt.savefig("lista_vs_ista.png", dpi=150)
print("plot saved.")

# print results
print("\nstats:")
print(f"lista final nmse (k={K}): {lista_trace[-1]:.2f} db")
for lam in lambdas:
    trace = traces[lam]
    target = lista_trace[-1]
    reached = next((i for i, v in enumerate(trace) if v <= target), None)
    
    iters_needed = reached if reached else f">{ista_iters}"
    print(f"ista (lam={lam}) nmse at k={K}: {trace[K-1]:.2f} db | iters to match lista: {iters_needed}")