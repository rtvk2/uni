![[image.png]]

benchmarking with past papers; why is it different?

xi, yi to nn → embedding/rep (vector)

why is simple mlp a poor choice:

number of params req too high → 200x200=40000 but 40Kx40K=16x10^8  
no spatial info

convolutions → filter size doesnt change with scale

pre-train and fine-tune generic network (weights initiialised) → reusability, few shot, zero shot (no need of training data)

cats vs dogs model to buses vs cars

can decide how much to freeze and how much to train in backprop

[vs algo unrolling](https://app.notion.com/p/vs-algo-unrolling-3948b8dbccb8800dbf82ccf4267604bc?pvs=21)

transformers → learn from raw large data

unsupervised is imp in long term → we learn by observing, not by being told the name of every object

2016: intelligence was a cake, unsupervised → cake, icing → supervised, reinforcement → cherry. but we dont know how to make the cake

2019: instead of unsupervised → self-supervised

learning a part from learning the surroundings

Marco saw a furry little wampimuk hiding in the tree

predicts: Marco → human, wampimuk → animal (from desc and surroundings)

find meanings from surrounding words with no explicit supervision → word2vec (word embedding)

look up space of supervision

SSL: rep learning that enables learning good data representation from unlabelled dataset; constructing supervised learning tasks out of unsupervised datasets → adv

data labelling is exp and high quality labeled dataset is limited  
learning good rep → easier to transfer useful info to a variety of downstream tasks

pre-trained model might be opensource; data isnt → security risks

two ways of ssl:

self prediction; contrastive learning

predict angle of rotation of perturbed image  
predict relative patch locations in image  
solving jigsaw puzzles  
predicting missing pixels (inpainting)

Transformers: Under the Surface

to be looked at as a framework rather than architecture; must be looked at from an input perspective

nlp → traditionally ml averse, but arose from it

language has smth that symbolic (symbol after symbol) ai doesn’t provide  
characters alone dont have meaning

word2vec → foundational shift

→ originated in 2017 and 2020 → vision; rn → everything

outline;

language and challenges (originating in language but applicable to other domains asw)  
transformers

positional embedding  
attention  
residual and normalisation  
FFNN  
MoE  
scaling

challenges:

long dependencies in language → can be seen in speech asw; shifts in phonetics

relationships in language:

anaphora; pronoun -: object lies at the forefront

i read the latest bestseller and i didnt enjoy ‘it’ as much  
traditional nlp → look back at 5~ sentences before

co-reference;

i didt vote for x because they aligned most with my

causal relation;

a happened therefore b happened; sometimes hidden in social understanding → i was late, i had a flat tire

long distant relation; the problem can be stated and then have a large discussion before relating the outcome (ex character introduced in 1st page to be from spain and then referenced later to be fluent in ____; would be spanish)

what allows arch to bring long distance relations closer is: Attention; (like paying attention to relevant stimuli (or parts) of the context)

→ implies that there is a scale of relevancy; attention score

problem for input field; can be aggregated

for images, we can divide into a grid and flattening them by concatenating the row segments → create a vector;

vision is bound laws of nature (specifically, octaves); language isnt bound by anything (has no physics)

where is person x in image; vector cant answer but if the whole input field can be seen at once, it can be answered; erases the unnecessary borderline

attention → take vector and get highest cosine similarity with other vectors

but what doest the vector signify?

given n-1 prev words, pred nth word; ref claude shannon

grew up in spain, fluent in ____

vector key and vector value

attention function takes those two and gives a result (can be any math function)

all attentions must sum up to 1; normalized

$max_{i|{words-in-vocab}} P( i| fluent -in)$

advantages of attention:

earlier it was very symbolic  
very human-like now  
solves bottleneck  
solves vanishing gradient  
some interpretability  
alignment in translation

limitations:

lack of parallelizability (RNNs)  
computation cost

transformers allowed paralllelization → self-attention

google couldnt use statistical models since they were beaten by neural models (but were very slow); like while typing, translation happened almost realtime. neural → had to wait

BERT based models → encoder

GPT family → decoder

masking → one way; look only at prev words. both encoder and decoder blocks are the same otherwise; done as the next word isnt gen yet, i/p is bidirectional (B in BERT)

in all cases, model starts with an embedding layer

when we observe the world, we observe in symbols; how to go from sym to math rep for this specific task? → sym i is associated with a row in a matrix

embedding is mult of symbol with weight matrix; error min in backprop

large words are broken into sub-words to reduce complexity

list with sub-words; i is the ith sub-word; each sub-word can be rep as a one hot vector

x→ embedding of x; E(x) → etc

postional embeddings:

three words: ram, hit, sham

ordering of words is imp in language (restrictive part); syntax/grammar

no free order (in most romance/germanic langs)/ relative free order (in some)

noun//verb modifiers must be attached  
causality  
temporality

sinusoidal pe:

allow model to easily learn to attend by rel positions;  
for any offset k, the

$PE_{pos,2i} = sin (pos/10000^{2i/d_{model}})$,  
$PE_{pos,2i+1} = cos (pos/10000^{2i+1/d_{model}})$

gains:

word position  
similar per as learned pe  
allows model to extrapolate to longer seq than the ones encountered during training; sinusoidal is periodic → csn be extended to 4000 tokens without losing much perf

drawbacks;

RoPE; plain embedding (first gpts);

integrate positional info into learning  
encodes ‘absolute’ position with a rotation matrix  
incorps the explicit rel position dep in self-attention formulation  
ram req of model → how many real nums are you storing  
rotate the affine-transformed word embedding vector by amount of angle multiples of position index; (like matrix transformation in robotics)

gains:

flexibility of seq length  
decaying inter-token dependency

drawbacks:

complexity

self-attention:

the animal didnt cross the street because it was too tired

gather the meaning from the relevant parts of the context

$attention(Q,K,V)= softmax({QK^T}/(d_k)^{1/2}V)$

simplest function:  
dot product of any two words → high value when cell values are similar;  
in embeddings, very low similarity

two transformations req:

question embedding Q

transform word embeddings with weight matrices

```
                            a, brown, cat…
```

$PE_i W_k$ → keys → k1, k2, k3..  
$PE_i W_q$ → queries → q1, q2, q3..  
$PE_i W_v$ → values → v1, v2, v3..

input embedding (at word level) → broken into three parts (through linear transform)

multi-head attention:

concat is same size; lin layer → vector mult matrix → weighted sum vect

original → 8 heads; could be 64 for large (giving model a chance to get more info)

drawbacks:

quadratic computation in self-attention  
high computation anf memoryn costs  
slow inference; extremely large KV (key & value vectors) cache  
redundant heads

alternatives;

mechanism changes

longformer  
grouped querry attention - LLamMA, Qwen  
Multi Head Latent Attention - DeepSeek, Kimi  
Log Linear Attention  
Ring Attention

compute optimisation

multi query attention

slow incremental inference (non-parallleliziable)

approach:

multi query heads; reduce kv pairs by having single shared k,v across heads

from $E * W_q,k,v$ * 8 to $E* W_q _8$ & $E_ W_k,v$

grouped query attention

approach:

MHA  
GQA  
convert a multihead checkpoint into a multi query checkpoint  
divide query heads into c1 grps each of which shares a single key head and value head

gains:

drawbacks:

more complex than MHA or MQA:

right grping (where do you avg) → optimal group size/management

multi head latent attention:

reduce kv cache  
mitigate perf degradation of MQA and GQA over MHA

matrices → rows of parameters → system of linear eqs;

low rank situation; large number of params but at any given time a lot of them arent useful (which are useful at other times)

say M_1000x1000, if M = A_r . B_r, we save a lot of space → LoRa (low rank approximation) → can be done as matrices are usually overparameterized

low-rank key-value joint compression

reason for DeepSeek/Qwen to be compact

gains:

reqs significantly smaller KV cache

during inference, MLA only needs to cache ci^KV  
only cei elements

![image.png](attachment:b9fb927b-8f8a-4ab3-a077-af327046c0cd:image.png)

residual connection (aka skip connection) and normalisation (new block)

{[PE+MHA(PE)]|normalisation+FF(MHA(PE))}|normalisation; using softmax

w → learns the plane; y =cw+b

residual connections:

train better  
smooths loss funct (error surface, dep on all params, also becomes smooth)

layer norm:

normalise to unit mean and s.d in each layer  
cuts down uniformative

w/o normalisation → exploding/vanishing gradients, slow conv, training instability

batch norm:

norm for 50-100 samples  
issues

layer norm:

norm across features

pre & post normalisation; trad → post

currently pre normalisation is done, as it turns out to be much better

→ attention itself benefits from the consistent range

activation functs;

gated linear units

computationally cheaper  
must not jave negative values so that it is differentiable

gaussian version of relu → gelu; smooth + differentiable

swish → x sigmoid (beta x)

swiglu

mixture of experts 33B-A3B

expert sizes  
routing algos  
training objectives

experts → sort of a misnomer

to get tokens, we use tokenizer; whiteplayer diff tokenizer techniques

if we consider word to be the token, and its not part of our data, we wont get the embeddings

BERT → non static contextual embedding method

tokens can be words, subwords or character. every token has an embedding which we get from

in lstm glu → sequential; therefore we dont need to give positional embedding. as position is preserved

not the case in transformer → need to add positional info of every token; various methods to get → comb of sinusoidal functions. rope embedding (2023-24)

add the word embeddings and postional embeddings → position-aware embedding → sent to encoder block → MMSA → FFNN. k,v

in decoder block, MMSA → (activations are sent to) cross attention → FFNN. q

when feeding input, we start with special token start.

w x embedding → unembedding layer; probability distribution; loss is cross entropy

ground truth embedding is one hot encoded version

all errors are computed in parallel, summed up and backprop updated

at inference time, it cannot be done parallely → sequential

masked matrix → lower triangle =1s, upper triangle =-inf → then softmax (e^-inf =0)

google BERT paper;

2018-20 people thought encoder alone is sufficient → encoder only model → entire sequence is known by the model

pre-training: hard to train entire model again → can be trained on info abt languages → customer can fine tune

mlm → masked language modelling

|||expect the model to produce ‘M’||expect the model to produce ‘M’||
|---|---|---|---|---|---|
|||||||
|<start>|I|am (replaced by <mask>)|going|to (replaced by <mask>)|school|
|||||||

loss is only computed for tokens that are masked.

multiple decisions to be made:

how many tokens to be masked  
which tokens to be masked

only k% (usually 15%) of the tokens need to be masked out of which:

80% of the time, replace with <mask>  
10% of the time, replace with random token  
10% of the time, mask with themselves → no mask

otherwise the model will only learn to predict a mask token instead of learning the semantics

cls

next sentence prediction;

give pair of sentences to model → s1,s2,s3,s4.. (pairs of sentences) → s2 follows s1 (positive samples) in doc and in some cases s2 does not follow s1

|||||||
|---|---|---|---|---|---|
||s1|s1||s2|s2|
|<start>|ipsum|lorem|<sep>|hi|hello|

task is a classification problem to check if positive or negative

classification loss and cross entropy loss are computed together

labelling data here is not needed to be done by humans → self supervised learning

supervised → there is some gradient or backprop

is BERT an llm model? → no, models with > 7B params → llm → was introduced in 2020 in gpt 3 paper

QA task → question and paragraph is given

say Q → what was the character name in Javaan?

- wiki doc of shahrukh khan is given; where vikram rathore is written somewhere

task is to output the word: vikram rathore

how to customise BERT to work for this task? →

|||||
|---|---|---|---|
|||||
|<start>|q|<sep>|a|

self attention → every token is aware of other tokens → cls is also aware of other tokens → if cls token wasnt present, where do we add the classification head? other tokens cant be used → embedding of other tokens has a bias of itself. since cls is not part of input → even with bias, doesnt hamper semantics.

introduce two classifiers:

one identifies the beginning of the chunk  
the other identifies the end of the chunk

for all tokens we see the result to be high prob for no for both; except for the term ‘vikram rathore’ tokens → we see no for end and beginning resp.

middle words need not be classified; say blue in ‘red blue green’

but adaptation irl was tougher → shift from encoder-only model to decoder-only model

at inference stage, for every token, 4 probabilities are produced→ cb+, cb-, ce+, ce- (beg and end).

how do we know the span? if we look at all yes probs and take the max for both beginning and end → but it may happen that they are at different pos  
find max (cb+ and ce+ probs? recheck)

decoder-only models:

encoder-decoder model:

t5 → text to text transfer transformer (2020)

in the same week, BART both employed same

corruption method → modify/corrupt input → various techniques (say A B C D E is input):

1. rotate tokens: C D E A B
2. drop tokens:
3. token masking
4. sentence permutation

whatever the corruption in input, the output must be A B C D E

in T5, they used only masking technique:

thank you for initing me to your party last week → thank you <x> me to your party <y> week; here the masked tokens are called sentinals → for every masking there is a unique sentinal. (in BERT there was jsut one sentinal)

output is to predict the mask for sentinal;

<x> for inviting <y> last <z>

(even the span is masked)

t5 overestimated that everything is a text to text problem. novelty wasnt in framework but rather data curation

(in gpt3 → the model is so big, we just need to finetune)

in 2021, they realised the model doesnt have the capability to actually understand the instruction → need another training to understand that

→ instruction fine tuning → instructor finetuned model by openai

one more training → alignment

new dataset → ins, resp pairs

say ins1 is summarise the paragraph; generally input is embedded into the instruction

automated creation of this type of dataset wasnt possible (new methods came up later)

weighted instruction turning (WIT)

compute loss only with respect to the resp tokens (not on top of ins tokens) → standard process

if loss was computed over ins → only teaching model how to respond, not how to understand instructions

trick: computed loss at both but alpha x ins loss, 1- alpha x resp loss → 7% boost in perf

getting the data:

flan 2021:

template 1:

premise →

template 2:

premise → can we infer the → hypothesis →

template 3

self-instruct:

start with 175 seed tasks → identify 1 instruction & 1 instance per task  
objective: generate new ins + examples for each ins (instances)

in-context learning (gpt3 paper):

write new ins → model must still now

ask for summarisation but it wasnt trained on that. give examples of the ins → the model should then be able to perform that task

(multilingual in-context learning paper; first paper on multi-lingual)

real ins + syn. ins → sample 8 → provide to the llm to generate more instructions and check similarity to existing set of generated ins.

different pipelines for classification and non-classification tasks

to identify, prompt gpt:

give classification label info (like positive, neutral, negative etc) and generate instances → output-first prompt

if not classification task → input-first prompt

no human intervention here → self instruct

agentic memory:

internal parametric memory  
retrieval as a memory (RAG paradigm)  
contextual memory (from prompt)

evol-instruct

two ways to generate complicated ins:

in-depth evolution:

add constraints  
deepening  
concretizing  
increase reasoning

in-breadth evolution:

enhance:  
topic coverage  
skill coverage

same difficulty of prompt + more info

orca:

idea in distillation → use larger model to generate data for smaller model for finetuning

in gpt 4 output is very small → not useful for ins finetuning

popular ins-tuned models on known datasets

flan t5 → used to finetune t5-11B  
alpaca 7B → finetuned on stanford alpaca dataset  
wizardlm (7B) → finetuned on wizardlm evol instruct  
mistral 7B-openorca → finetuned on openorca dataset (highly curated subset of gpt 4 augmented reasoning traces)

orchestrator → also an llm → that decides which llms are needed to respond to an input; say in a framework having 4 models. some sort of rll / how to get various llms to interact. the final output is again decided by it; multi-expert system, multi-agent orchestration papers

parameter efficient fine tuning:

why? models are so big (not enough data to tune all params) → pick some layers/params/ add new params/ reparameterization to finetune

types:

additive peft  
selective peft  
reparameterization

additive:

adapter; plug and play modules

add more params; insig to actual total number of params

adapter → new module inserted to the llm; essentially an mlp b/w encoder & decoder

input to adapter is output of FFNN and output of adapter is input of MMSA

FF down and up projections (down towards adapter) but FFN traditionally looks inverse of that → this is to ensure same dimensions of input and output using projections

d → 2d → d; has more params → needed as these are the memories of transformers; more size of ffn more storage → 70% of params used up by ffn

but

d → d/2 → d; in adapters as we want to use less params; param efficient finetuning

adapter can be attached anywhere b/w two layers and only finetune the adapters, keeping the other params frozen → only adapter weights are updated in backprop

sparse autoencoders

in BERT with 3.5% additional params (adapters) → achieves 99.6% perf of full finetuning

when transfering, only adapters need to be sent → plug and play

pre-fix tuning;

prompt tuning → prepend another vector (not part of original input) to position-aware embeddings → sent to encoder-decoder model; prepended part acts as an adapter → loss is computed along these params; model params frozen

instead of using adapter, prepend a vector to every layer (instead of just input like in prompt-tuning)

transfer only requires prepended vectors

same prepended vector for different instances of task

additional: task vectors/ angled neurons; we know neurons for english and for hindi, steer only the neurons required for task (give imp).

selective methods:

instead of adding new params, select subset of existing params

two types:

structured:

identify a block a transformer like attention head, FFN  
update specific components (like attention head weights)

bitfit (zaken et al):

only update the bias terms of transformer layers

drop out isnt used these days; doesnt improve training process proved empirically

unstructured;

haphazardly select params from different parts of a transformer; anywhere in any layer

FISH mask (sung et al):

add noise to param and see how it impacts the final probability distribution

if kl divergence of output for new distribution is high tjen the param is imp as it impacts the model highly

if the delta tends ot zero then this equation points to the Fisher importance matrix

fisher importance score (equivalent to kl divergence calc)

F → E E

sample an input from input space then sample output from distribution (empirically expectation calc isnt possible)

using monte carlo sampling → sample x from distribution and find gradient.

for every theta there is a scalar value → higher = more imp

PaFI (liao et al.):

follow-up paper

take magnitude of param and if mag is lower → can be finetuned more

ID3 (agarwal et al):

both heuristics and mag are imp

directly prop to grad and inverse prop to mag

params were selected beforehand and finetuned in other methods; hre params are selected on the fly.

say budget is b → can finetune b number of params (same in all selective methods)

iterate t number of times → b/t number of params selected in every iteration

diff from finetuning all b params every iteration → effectiive number of finetuning is much lesser

when b/t params are selected → affect other params while finetuning; so in the second iteration pick b/t params that are affected (not in case of other methods)

reparameterization methods

modify the form of paras to allow efficient learning

LoRa:

low rank approximation

say w (can be one layer of matrix, one self attention block, anything)

qhen finetuning, we modify w to w+delta w; delta w is smth we are searching for

any matrix can be decomposed into $A_{p,r} B_{r,p}$; r <<< p

let delta $w = A B$

for p=100, 100x100 ≠ 100 x 2 and 2 x 100

→ $w^T x → (w + A. B)^T x$

LoRa is beautiful since it can be applied ot any part of transformer

it can be used in combination with selective methods like pafi/

problem:

unstable to changes in hyperparams → new methods AdaLoRa (LoRa gave equal imp to all layers, here we adaptively choose what ot give imp to)

DoRa (every matrix M can be converted into combination of magnitude and direction adn train them separately)

MonteCLoRa (bayesian monte carlo approximation of matrix to reduce sensitivity to hyperparams)

llm alignment:

topics:

llm training stages  
crash course on rl  
introduction to rlhf  
deep dive into rlhf: ins fine tuning; rewards modeling; rl algos  
direct alignment algos

[rlhfbook.com](http://rlhfbook.com)

llm training stages:

pre-training:

objective: next token prediction (autoregressive language modeling)  
data: mostly unlabeled  
scale: params from 2B to >1T+; compute: thousands to millions of gpu/tpu hrs  
model learns: grammar, syntax, semantics, factual knowledge etc.  
output: raw base model (can generate fluent text but is not aligned- maybe unhelpful, toxic, repetitive etc)

mid-training:

objective: still mostly next-token prediction but on curated higher quality data

goal: to boost the reasoning, reduce hallucinations, extend context length, strengthen multilingual performance, prepare for better alignment

post-training (alignment):

final end-user aligned model

alignment → turns capable but raw base model to a helpful safe and usable assistant  
uses much lesser compute than other stages

post-training;

two substages:

supervised fine-tuning (sft) / instruction tuning;

objective: teaches model to follow instructions  
data: ins resp pairs (human+synthetic)  
method: finetune with next token pred on these pairs  
outcome: instruct model; can still be biased, overly verbose

preference optimization / reinforcement learning (rl):

rlhf (rl from human feedback): humans rank model outputs → train a reward model → optimize the policy with ppo or similar

dpo (direct preference optimization): simpler, more stable than rlhf tjat directly optimization on pref pairs (chosen vs rejected)

kto, orpo, spin → newer variants

objective: make it helpful, honest, harmless (HHH), less toxic, better at complex inst.

additional techniques:

safety/refusal training → refuse harmful reqs  
constitutional ai/ self-critique → model critiques its own outputs  
tool use / function calling → training  
long-context / agentic capabilities  
personality/ tone shaping (like grok on twitter)

output: final chat/instruct model that end-user interacts with

data is king across all stages (esp in mid and post)  
heavy use of syn data generated by stronger models  
mid-training is becoming more prominent and longer (aka pre-training 2.0)  
test-time scaling and reasoning models like o1 add extra inference-time compute after post-training  
mixture-of-experts (MoE)  
increasing focus on multi-modal  
pipeline is iterative

crash course on rl:

getting the treasure; maze (with rewards and restrictions) without other info, reach the goal  
no supervision/but learn from interactions with env  
action may have immediate consequence or much later (delayed rewards); like in chess → we know if we win or lose only at the end → assign credit for seq of actions to the end  
trial-and-error search + exploration vs exploitation dilemma (power-up/credit in maze, but maybe in unexplored area there might be a better power-up/credit)

rl cycle:

observe → evaluate → act → react

→ new obs + reward → evaluate → act → react → …

time step (or epoch)  
experience tuple: (s, a, s’, r)  
episodic task (having natural ending):  
episode: seq of time steps from beginning to end  
continuours task: never ending  
return: sum of rewards collected in a single episode

rl is about complex seq decision making problems under uncertainity

elements of deep reinforcement learning:

policy: mapping from perceived states of the environment to actions to be taken

reward signal: defines the goal of a RL problem. an agent receives reward from the environment at each step. The goal is to maximize the total reward.

Value of a State: Total reward that an agent can expect to accumulate in the long

model:

Markov Decision Process (MDP):

S: state space]

in standard rl:

s: state  
a: action

in rlhf for llms:

x: prompt/query  
y: response  
note: entire response (seq of all tokens) is taken as one action

in rl → environment gave the reward; static; state transitions

in rlhf → first reward model is to be learned which is then used with the llm; dynamic; no state transitions

introduction to rlhf:

rlhf became popular when used to post train chatgpt model  
subset of preference

rlhf pipeline:

phase 1: supervised fine tuning (sft)  
phase 2: reward model training  
phase 3: reinforcement learning (ppo)

policy here is the model itself.

rlhf has also been applied to:

drl  
summarization  
following instructions

rlhf works on elicitation theory of post-training

alternative theory: superficial alignment hypothesis (SAH) proposed in LIMA

partially true for easy alignment  
less true for capability enhancement  
boundary between style and capability is blurry

rlhf history (pre 2019);

TAMER  
COACH  
deep reinforcement learning from human preferences

landmark moment → chatgpt

rlhf optimization tools:

reward modeling  
instruction finetuning  
rejection sampling  
policy gradients  
direct alignment algos  
modern rlhf → ins finetuning → mix of other opts

rlhf pipeline:

inst. finetuning (IFT) or supervised finetuning (SFT)

why IFT is done as post-processing step?

step 1; dataset preparation (most critical step)  
step 2: data formatting and pre-processing  
step 3: model training

output: instruct tuned model → later aligned with preference optimization

advantages of rlhf over just ift

challenge → rlhf length bias → longer resp rewarded more

reward modeling

most popular - Bradley-Terry (BT) reward model (standard rm)

assumes thst each item has a latent strength p_i > 0 and that observed preferences are a noisy reflection of these underlying strengths  
unbounded scores are reparameterized

preference margin loss

strong → larger gap b/w two scores

llama2

BT model variants:

instruct gpt reward model

llm generates a variable number k varying from 4 to 9 responses for each prompt  
humans annotators rank them  
update

plackett luce model

for prompt xi, llm genrates k responses  
labellers are used too rank preferences; 0 being the most preferred response

for k=2 → becomes the BT model

other reward models:

outcome reward models (orm)

used for reasoning heavy tasks  
training data:

for prompt → generate two resps  
each resp gets verified as correct or incorrect using an automatic verifier

model trained differently;

per token response as opposed to total calc in standard rm

process reward models (prm)

used for chain-of-thought reasoning process; outputs scores at each step

preds tend to be -1, 0, 1

generative reward modeling:

prompt the lm to act as a judge of human preferences or in other evaluation settings

rl algos: policy gradient

this class of algo is used in rlhf;

proximal policy optimization (PPO), group relative policy (GRPO), REINFORCE

rlhf reqs 4 models:

reward model  
policy (llm being trained)  
a copy of the policy before RL serves as the ref model for computing a KL penalty (the params of this are frozen

policy gradient algos:

interested in learning a parameterized policy (llm itself)  
policy is optimised through a cost funct → J  
gradient of cost function is calculated so that the policy can be learned via maximization of cost function

aims to maximise return

policy update

gradient of expectation of a function

log derivative

proximal policy optimization (ppo)

computation involve two main terms

a standard policy gradient with a learned advantage  
a clipped policy

grpo → deepseekmath, deepseek v3, r1

dpo