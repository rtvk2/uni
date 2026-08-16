numpy recap
https://www.youtube.com/watch?v=QUT1VHiLmmI
https://www.youtube.com/watch?v=VXU4LSAQDSc

pillow
https://www.youtube.com/watch?v=5QR-dG68eNE

---

# NumPy Quick Reference

NumPy (Numerical Python) is optimized for high-performance operations on multi-dimensional arrays, using contiguous memory and vectorization (SIMD) to bypass the overhead of native Python lists.

## Setup & Basics
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Install** | `pip install numpy` | Installs the NumPy library via terminal. |
| **Import** | `import numpy as np` | Standard import convention. |

## Array Creation & Types
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **1D Array** | `np.array([1, 2, 3])` | Creates a standard 1D array from a list. |
| **2D Array** | `np.array([[1.0, 2.0], [3.0, 4.0]])` | Creates a 2D matrix from nested lists. |
| **Zeros Matrix** | `np.zeros((2, 3))` | Initializes a 2x3 matrix filled with `0`. |
| **Ones Matrix** | `np.ones((4, 2, 2), dtype='int32')` | Initializes a 3D array of `1`s with a specific data type. |
| **Full Matrix** | `np.full((2, 2), 99)` | Initializes a 2x2 matrix where every value is `99`. |
| **Full Like** | `np.full_like(a, 4)` | Creates a new array with the exact shape as array `a`, filled with `4`s. |
| **Identity Matrix** | `np.identity(3)` | Creates a 3x3 square identity matrix (1s on the diagonal, 0s elsewhere). |
| **Type Casting** | `a.astype('int32')` | Explicitly casts the array's data to a new type (e.g., float to int). |

## Array Inspection
| Property | Code / Command | Description |
| :--- | :--- | :--- |
| **Dimensions** | `a.ndim` | Returns the number of dimensions (e.g., 1, 2, 3). |
| **Shape** | `a.shape` | Returns a tuple representing the array dimensions (e.g., `(2, 3)`). |
| **Data Type** | `a.dtype` | Returns the data type of the elements (e.g., `int32`, `float64`). |
| **Item Size** | `a.itemsize` | Returns the size (in bytes) of a single element. |
| **Total Elements** | `a.size` | Returns the total count of elements in the array. |
| **Total Memory** | `a.nbytes` | Returns the total bytes consumed by the entire array. |

## Indexing, Slicing & Filtering
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Specific Element** | `a[1, 5]` | Accesses the element at row index 1, column index 5. |
| **Specific Row/Col** | `a[0, :]` / `a[:, 2]` | Retrieves all elements in the 0th row / 2nd column. |
| **Advanced Slicing** | `a[0, 1:6:2]` | Slices the 0th row from index 1 to 5, stepping by 2 (`[start:end:step]`). |
| **List Indexing** | `a[[0, 1, 8]]` | Passes a list of indices to grab specific, non-sequential elements. |
| **Boolean Masking** | `a[a > 50]` | Returns a 1D array filtered to only include values meeting the condition. |
| **Multi-Condition** | `a[(a > 50) & (a < 100)]`| Uses `&` (and), `\|` (or), and `~` (not) for complex masking (requires parentheses). |
| **Where (Filter)** | `np.where(a >= 18, a, 0)` | If condition is true, keep element; if false, replace with `0`. Preserves array shape. |
| **Any / All** | `np.any(a > 50)` / `np.all(a > 50)` | Returns a boolean if *any* element or *all* elements meet the condition. |

## Mathematics & Linear Algebra
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Constants** | `np.pi` | Returns the mathematical constant Pi. |
| **Element-wise Math**| `a + 2`, `a * 2`, `a ** 2` | Applies basic arithmetic to every element in the array. |
| **Rounding** | `np.round(a)`, `np.floor(a)` | Rounds elements to the nearest integer, or rounds down (`ceil` to round up). |
| **Square Root** | `np.sqrt(a)` | Returns the positive square root of every element. |
| **Trigonometry** | `np.sin(a)`, `np.cos(a)` | Computes the sine/cosine for all elements. |
| **Matrix Multiply** | `np.matmul(a, b)` | Multiplies two matrices mathematically (dot product style). |
| **Determinant** | `np.linalg.det(c)` | Calculates the determinant of a square matrix. |

## Statistics (Aggregate Functions)
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Min / Max** | `np.min(stats)` / `np.max(stats)` | Returns the lowest or highest value in the array. |
| **Index of Min/Max** | `np.argmin(stats)`, `np.argmax(stats)`| Returns the *index position* of the minimum or maximum value. |
| **Sum** | `np.sum(stats)` | Sums all elements in the array. |
| **Mean (Average)** | `np.mean(stats)` | Calculates the arithmetic mean of the array. |
| **Std Dev / Variance**| `np.std(stats)`, `np.var(stats)` | Computes the standard deviation and variance (measures of data spread). |
| **Axis Arguments** | `np.min(stats, axis=1)` | Use `axis=0` for column-wise operations, `axis=1` for row-wise. |

## Array Manipulation (Reorganizing)
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Reshape** | `a.reshape((8, 1))` | Changes dimensions without altering data (total element counts must match). |
| **Vertical Stack** | `np.vstack([v1, v2])` | Stacks arrays vertically (on top of each other). |
| **Horizontal Stack**| `np.hstack((h1, h2))` | Stacks arrays horizontally (side-by-side). |
| **Repeat Elements** | `np.repeat(arr, 3, axis=0)`| Repeats elements of an array a specified number of times along a given axis. |

## Random Generation
*Modern NumPy recommends using the `default_rng` generator over the older `np.random.rand` methods.*

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Init Generator** | `rng = np.random.default_rng(seed=1)`| Initializes the modern random generator. `seed` guarantees reproducible results. |
| **Random Integers** | `rng.integers(low=1, high=7, size=3)` | Generates 3 random integers from 1 to 6 (high is exclusive). |
| **Random Floats** | `rng.uniform(-1, 1, size=(3,2))` | Generates a 3x2 matrix of random floats evenly distributed between -1 and 1. |
| **Random Choice** | `rng.choice(fruits, size=3)` | Randomly selects elements from an existing 1D array or list. |
| **Shuffle Array** | `rng.shuffle(a)` | Randomly shuffles the elements of an array *in-place*. |

## Core Concepts
| Concept | Description |
| :--- | :--- |
| **Broadcasting** | Allows NumPy to perform math on arrays with different shapes by virtually expanding the smaller array. Two dimensions are compatible if they are equal, or if one of them is `1` (evaluated from right to left). If incompatible, NumPy throws a `ValueError`. |

## Miscellaneous
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Load File Data** | `np.genfromtxt('data.txt', delimiter=',')`| Reads delimited text files directly into an array. |
| **Deep Copy** | `b = a.copy()` | Safely duplicates an array (prevents modifying the original by reference). |

---

# Pillow (PIL) Quick Reference

Pillow is a versatile Python library used for opening, manipulating, and saving many different image file formats.

## Setup & Basics
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Install** | `pip install Pillow` | Installs the Pillow library via terminal. |
| **Import** | `from PIL import Image` | Standard import for the core Image module. |
| **Open Image** | `img = Image.open("pic.jpg")` | Loads an existing image from a file path. |
| **Create Blank** | `img = Image.new('RGBA', (100, 100))` | Creates a new blank image with specified format and `(width, height)`. |
| **Show Image** | `img.show()` | Opens the image using the OS default image viewer. |
| **Save Image** | `img.save('out.png')` | Saves the image (format is inferred from the extension). |

## Image Information
| Property | Code / Command | Description |
| :--- | :--- | :--- |
| **Dimensions** | `img.size` | Returns a tuple `(width, height)`. |
| **File Path** | `img.filename` | Returns the source file path/name. |
| **Format** | `img.format` | Returns the image format (e.g., `JPEG`, `PNG`). |
| **Color Mode** | `img.mode` | Returns the current color mode (e.g., `RGB`, `RGBA`, `L`). |

## Basic Manipulations
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Rotate** | `img.rotate(60, expand=True, fillcolor=(255,0,0))`| Rotates image by degrees. `expand=True` prevents corner clipping. |
| **Crop** | `img.crop((left, top, right, bottom))` | Crops using a bounding box tuple. |
| **Flip Horizontal** | `img.transpose(Image.Transpose.FLIP_LEFT_RIGHT)`| Mirrors the image across the Y-axis. |
| **Flip Vertical** | `img.transpose(Image.Transpose.FLIP_TOP_BOTTOM)` | Mirrors the image across the X-axis. |
| **Resize** | `img.resize((new_w, new_h))` | Resizes image to exact dimensions (can skew aspect ratio). |

## Color Modes (`convert`)
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **1-Bit (B&W)** | `img.convert('1')` | Converts to purely black and white (no grey). |
| **8-Bit Grayscale**| `img.convert('L')` | Converts to standard grayscale (shades of grey 0-255). |
| **Palette Mode** | `img.convert('P')` | Limits the image to 256 mapped colors (efficient storage). |
| **Alpha Channel** | `img.convert('RGBA')` | Converts to Red, Green, Blue, and Alpha (transparency) channels. |

## Image Analysis, Pixels, & Palettes
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Get Pixel Color** | `img.getpixel((0, 0))` | Returns the color tuple at a specific `(x, y)` coordinate. |
| **Set Pixel Color** | `img.putpixel((100, 100), (255, 0, 0))` | Overwrites the color of a specific pixel. |
| **List Colors** | `img.getcolors(maxcolors=...)` | Returns a list of colors used in the image and their counts. |
| **Get Image Bands** | `img.getbands()` | Returns a tuple containing the names of the image's bands (e.g., `('R', 'G', 'B')`). |
| **Extract Channel** | `img.getchannel('R')` | Extracts a single band (like Red) as a separate grayscale image. |
| **Adaptive Palette** | `img.convert('P', palette=Image.ADAPTIVE, colors=16)`| Converts to Palette mode with a dynamically generated, optimized color palette. |
| **Get/Put Palette** | `palette.getpalette()` / `palette.putpalette(new)` | Extracts or replaces the raw list of color values mapping a palette image. |
| **NumPy Conversion**| `np.array(img, dtype=int)` | Converts an image directly into a NumPy array for mathematical operations. |

## Enhancements & Filters
*Requires:* `from PIL import ImageEnhance, ImageFilter`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Enhance Color** | `ImageEnhance.Color(img).enhance(2)` | Adjusts vibrance/saturation (1.0 is original, >1 increases). |
| **Enhance Contrast**| `ImageEnhance.Contrast(img).enhance(2)` | Adjusts visual contrast. |
| **Enhance Brightness**| `ImageEnhance.Brightness(img).enhance(2)` | Adjusts total image brightness. |
| **Enhance Sharpness**| `ImageEnhance.Sharpness(img).enhance(5)` | Adjusts image sharpness. |
| **Basic Filters** | `img.filter(ImageFilter.EMBOSS)` | Standard filters: `BLUR`, `CONTOUR`, `EMBOSS`, `FIND_EDGES`, `DETAIL`, `SHARPEN`, `SMOOTH`. |
| **Rank Filters** | `img.filter(ImageFilter.MinFilter(size=25))` | Filters pixels by ranking them in a window (`MinFilter`, `MedianFilter`, `MaxFilter`). |
| **Advanced Blurs** | `img.filter(ImageFilter.BoxBlur(10))` | Applies a radius-based blur (also: `GaussianBlur(5)`). |

## ImageOps (Ready-made Operations)
*Requires:* `from PIL import ImageOps`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Auto Contrast** | `ImageOps.autocontrast(img, cutoff=5)` | Normalizes image contrast automatically. |
| **Equalize** | `ImageOps.equalize(img)` | Equalizes the image histogram to create uniform contrast. |
| **Invert** | `ImageOps.invert(img)` | Inverts image colors (creates a negative). |
| **Solarize** | `ImageOps.solarize(img, threshold=100)` | Inverts all pixel values above a specific threshold. |
| **Posterize** | `ImageOps.posterize(img, bits=2)` | Reduces color depth to a specific number of bits. |
| **Grayscale** | `ImageOps.grayscale(img)` | Quick method to convert an image to grayscale. |
| **Colorize** | `ImageOps.colorize(img.convert('L'), black="blue", white="red")`| Maps a grayscale image to a custom two-color gradient. |
| **Mirror/Flip** | `ImageOps.mirror(img)` / `ImageOps.flip(img)` | Quick alternative to `transpose` for mirroring/flipping. |
| **Scale** | `ImageOps.scale(img, factor=0.4)` | Scales the image uniformly while maintaining the aspect ratio. |
| **Expand (Border)** | `ImageOps.expand(img, border=10, fill='red')` | Adds a colored border around the outside of the image. |
| **Pad Image** | `ImageOps.pad(img, size=(1200, 400))` | Pads the image to the requested size while maintaining the original aspect ratio. |
| **Fit Image** | `ImageOps.fit(img, size=(600, 600))` | Crops and resizes the image to exactly fit the requested size without distorting. |
| **Ops Crop** | `ImageOps.crop(img, border=25)` | Removes a uniform border from all sides of the image. |
| **Deform Mesh** | `ImageOps.deform(img, Deformer())` | Warps an image using a custom mesh class that maps source shapes to target rectangles. |

## ImageChops (Channel Operations)
*Requires:* `from PIL import ImageChops`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Blend Modes** | `ImageChops.overlay(img1, img2)` | Composites two images. Includes `overlay`, `darker`, `lighter`, `soft_light`, `hard_light`, `difference`, `screen`. |
| **Math Add/Sub** | `ImageChops.add(img1, img2, scale=2.0, offset=100)`| Adds or `subtract`s pixel values between images, applying optional scale and offset. |
| **Modulo Arithmetic**| `ImageChops.add_modulo(img1, img2)` | Adds pixel values but wraps around if they exceed 255. |
| **Logical Ops** | `ImageChops.logical_and(img1, img2)` | Applies boolean math (`logical_and`, `logical_or`) between two 1-bit images. |

## Combining & Masking
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Blend** | `Image.blend(img1, img2, alpha=0.5)` | Blends two identically sized images based on an alpha value. |
| **Paste** | `bg.paste(fg, (x, y), mask=mask_img)` | Overlays `fg` onto `bg`. If `mask` is provided, transparent parts stay clear. |
| **Composite** | `Image.composite(img1, img2, mask)` | Combines two identically sized images using a mask to dictate visibility. |
| **Alpha Composite** | `Image.alpha_composite(img_rgba, mask_rgba)` | Composites an RGBA image onto another RGBA image based on alpha transparency. |

## Drawing & Text
*Requires:* `from PIL import ImageDraw, ImageFont`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Copy Image** | `img_draw = img.copy()` | Creates a duplicate in memory (crucial before drawing to preserve the original). |
| **Init Canvas** | `draw = ImageDraw.Draw(img_draw)` | Creates a drawing object tied to an image. |
| **Draw Rectangle** | `draw.rectangle((left, top, right, bottom), fill='red', outline='yellow', width=5)` | Draws a colored rectangle. |
| **Draw Ellipse** | `draw.ellipse((box), fill='blue')` | Draws an ellipse bounded by the specified box coordinates. |
| **Draw Polygon** | `draw.polygon(((x1,y1), (x2,y2), ...), fill='blue')` | Connects multiple points and fills the inner shape. |
| **Draw Line** | `draw.line(((x1,y1), (x2,y2)), fill='black', width=10, joint='curve')`| Draws a line connecting specified points. `joint` specifies connection style. |
| **Draw Curves** | `draw.arc(...)`, `draw.chord(...)`, `draw.pieslice(...)`| Draws curved shapes based on start/end angles within a bounding box. |
| **Init Font** | `font = ImageFont.truetype('font.ttf', size=80)` | Loads a TTF font file for text drawing. |
| **Draw Text** | `draw.text((x, y), "Text", fill="blue", font=font, align='center', anchor='mm')`| Writes text at `(x,y)`. `align` controls multiline alignment; `anchor` controls origin placement. |
---

# Pillow (PIL) Quick Reference

Pillow is a versatile Python library used for opening, manipulating, and saving many different image file formats.

## Setup & Basics
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Install** | `pip install Pillow` | Installs the Pillow library via terminal. |
| **Import** | `from PIL import Image` | Standard import for the core Image module. |
| **Open Image** | `img = Image.open("pic.jpg")` | Loads an existing image from a file path. |
| **Create Blank** | `img = Image.new('RGBA', (100, 100))` | Creates a new blank image with specified format and `(width, height)`. |
| **Show Image** | `img.show()` | Opens the image using the OS default image viewer. |
| **Save Image** | `img.save('out.png')` | Saves the image (format is inferred from the extension). |

## Image Information
| Property | Code / Command | Description |
| :--- | :--- | :--- |
| **Dimensions** | `img.size` | Returns a tuple `(width, height)`. |
| **File Path** | `img.filename` | Returns the source file path/name. |
| **Format** | `img.format` | Returns the image format (e.g., `JPEG`, `PNG`). |
| **Color Mode** | `img.mode` | Returns the current color mode (e.g., `RGB`, `RGBA`, `L`). |

## Basic Manipulations
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Rotate** | `img.rotate(60, expand=True, fillcolor=(255,0,0))`| Rotates image by degrees. `expand=True` prevents corner clipping. |
| **Crop** | `img.crop((left, top, right, bottom))` | Crops using a bounding box tuple. |
| **Flip Horizontal** | `img.transpose(Image.Transpose.FLIP_LEFT_RIGHT)`| Mirrors the image across the Y-axis. |
| **Flip Vertical** | `img.transpose(Image.Transpose.FLIP_TOP_BOTTOM)` | Mirrors the image across the X-axis. |
| **Resize** | `img.resize((new_w, new_h))` | Resizes image to exact dimensions (can skew aspect ratio). |

## Color Modes (`convert`)
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **1-Bit (B&W)** | `img.convert('1')` | Converts to purely black and white (no grey). |
| **8-Bit Grayscale**| `img.convert('L')` | Converts to standard grayscale (shades of grey 0-255). |
| **Palette Mode** | `img.convert('P')` | Limits the image to 256 mapped colors (efficient storage). |
| **Alpha Channel** | `img.convert('RGBA')` | Converts to Red, Green, Blue, and Alpha (transparency) channels. |

## Image Analysis, Pixels, & Palettes
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Get Pixel Color** | `img.getpixel((0, 0))` | Returns the color tuple at a specific `(x, y)` coordinate. |
| **Set Pixel Color** | `img.putpixel((100, 100), (255, 0, 0))` | Overwrites the color of a specific pixel. |
| **List Colors** | `img.getcolors(maxcolors=...)` | Returns a list of colors used in the image and their counts. |
| **Get Image Bands** | `img.getbands()` | Returns a tuple containing the names of the image's bands (e.g., `('R', 'G', 'B')`). |
| **Extract Channel** | `img.getchannel('R')` | Extracts a single band (like Red) as a separate grayscale image. |
| **Adaptive Palette** | `img.convert('P', palette=Image.ADAPTIVE, colors=16)`| Converts to Palette mode with a dynamically generated, optimized color palette. |
| **Get/Put Palette** | `palette.getpalette()` / `palette.putpalette(new)` | Extracts or replaces the raw list of color values mapping a palette image. |
| **NumPy Conversion**| `np.array(img, dtype=int)` | Converts an image directly into a NumPy array for mathematical operations. |

## Enhancements & Filters
*Requires:* `from PIL import ImageEnhance, ImageFilter`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Enhance Color** | `ImageEnhance.Color(img).enhance(2)` | Adjusts vibrance/saturation (1.0 is original, >1 increases). |
| **Enhance Contrast**| `ImageEnhance.Contrast(img).enhance(2)` | Adjusts visual contrast. |
| **Enhance Brightness**| `ImageEnhance.Brightness(img).enhance(2)` | Adjusts total image brightness. |
| **Enhance Sharpness**| `ImageEnhance.Sharpness(img).enhance(5)` | Adjusts image sharpness. |
| **Basic Filters** | `img.filter(ImageFilter.EMBOSS)` | Standard filters: `BLUR`, `CONTOUR`, `EMBOSS`, `FIND_EDGES`, `DETAIL`, `SHARPEN`, `SMOOTH`. |
| **Rank Filters** | `img.filter(ImageFilter.MinFilter(size=25))` | Filters pixels by ranking them in a window (`MinFilter`, `MedianFilter`, `MaxFilter`). |
| **Advanced Blurs** | `img.filter(ImageFilter.BoxBlur(10))` | Applies a radius-based blur (also: `GaussianBlur(5)`). |

## ImageOps (Ready-made Operations)
*Requires:* `from PIL import ImageOps`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Auto Contrast** | `ImageOps.autocontrast(img, cutoff=5)` | Normalizes image contrast automatically. |
| **Equalize** | `ImageOps.equalize(img)` | Equalizes the image histogram to create uniform contrast. |
| **Invert** | `ImageOps.invert(img)` | Inverts image colors (creates a negative). |
| **Solarize** | `ImageOps.solarize(img, threshold=100)` | Inverts all pixel values above a specific threshold. |
| **Posterize** | `ImageOps.posterize(img, bits=2)` | Reduces color depth to a specific number of bits. |
| **Grayscale** | `ImageOps.grayscale(img)` | Quick method to convert an image to grayscale. |
| **Colorize** | `ImageOps.colorize(img.convert('L'), black="blue", white="red")`| Maps a grayscale image to a custom two-color gradient. |
| **Mirror/Flip** | `ImageOps.mirror(img)` / `ImageOps.flip(img)` | Quick alternative to `transpose` for mirroring/flipping. |
| **Scale** | `ImageOps.scale(img, factor=0.4)` | Scales the image uniformly while maintaining the aspect ratio. |
| **Expand (Border)** | `ImageOps.expand(img, border=10, fill='red')` | Adds a colored border around the outside of the image. |
| **Pad Image** | `ImageOps.pad(img, size=(1200, 400))` | Pads the image to the requested size while maintaining the original aspect ratio. |
| **Fit Image** | `ImageOps.fit(img, size=(600, 600))` | Crops and resizes the image to exactly fit the requested size without distorting. |
| **Ops Crop** | `ImageOps.crop(img, border=25)` | Removes a uniform border from all sides of the image. |
| **Deform Mesh** | `ImageOps.deform(img, Deformer())` | Warps an image using a custom mesh class that maps source shapes to target rectangles. |

## ImageChops (Channel Operations)
*Requires:* `from PIL import ImageChops`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Blend Modes** | `ImageChops.overlay(img1, img2)` | Composites two images. Includes `overlay`, `darker`, `lighter`, `soft_light`, `hard_light`, `difference`, `screen`. |
| **Math Add/Sub** | `ImageChops.add(img1, img2, scale=2.0, offset=100)`| Adds or `subtract`s pixel values between images, applying optional scale and offset. |
| **Modulo Arithmetic**| `ImageChops.add_modulo(img1, img2)` | Adds pixel values but wraps around if they exceed 255. |
| **Logical Ops** | `ImageChops.logical_and(img1, img2)` | Applies boolean math (`logical_and`, `logical_or`) between two 1-bit images. |

## Combining & Masking
| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Blend** | `Image.blend(img1, img2, alpha=0.5)` | Blends two identically sized images based on an alpha value. |
| **Paste** | `bg.paste(fg, (x, y), mask=mask_img)` | Overlays `fg` onto `bg`. If `mask` is provided, transparent parts stay clear. |
| **Composite** | `Image.composite(img1, img2, mask)` | Combines two identically sized images using a mask to dictate visibility. |
| **Alpha Composite** | `Image.alpha_composite(img_rgba, mask_rgba)` | Composites an RGBA image onto another RGBA image based on alpha transparency. |

## Drawing & Text
*Requires:* `from PIL import ImageDraw, ImageFont`

| Operation | Code / Command | Description |
| :--- | :--- | :--- |
| **Copy Image** | `img_draw = img.copy()` | Creates a duplicate in memory (crucial before drawing to preserve the original). |
| **Init Canvas** | `draw = ImageDraw.Draw(img_draw)` | Creates a drawing object tied to an image. |
| **Draw Rectangle** | `draw.rectangle((left, top, right, bottom), fill='red', outline='yellow', width=5)` | Draws a colored rectangle. |
| **Draw Ellipse** | `draw.ellipse((box), fill='blue')` | Draws an ellipse bounded by the specified box coordinates. |
| **Draw Polygon** | `draw.polygon(((x1,y1), (x2,y2), ...), fill='blue')` | Connects multiple points and fills the inner shape. |
| **Draw Line** | `draw.line(((x1,y1), (x2,y2)), fill='black', width=10, joint='curve')`| Draws a line connecting specified points. `joint` specifies connection style. |
| **Draw Curves** | `draw.arc(...)`, `draw.chord(...)`, `draw.pieslice(...)`| Draws curved shapes based on start/end angles within a bounding box. |
| **Init Font** | `font = ImageFont.truetype('font.ttf', size=80)` | Loads a TTF font file for text drawing. |
| **Draw Text** | `draw.text((x, y), "Text", fill="blue", font=font, align='center', anchor='mm')`| Writes text at `(x,y)`. `align` controls multiline alignment; `anchor` controls origin placement. |

FT Quick Ref:

### Core CTFT Pairs

|**Signal x(t)**|**Transform X(jω)**|**Quick Derivation / Mental Trigger**|
|---|---|---|
|$\delta(t)$|$1$|$\int_{-\infty}^{\infty} \delta(t) e^{-j\omega t} dt$. The delta function sifts out the value at $t=0$, which is $e^0 = 1$.|
|$1$|$2\pi\delta(\omega)$|**Duality:** If a pulse in time is flat in frequency, flat in time is a pulse in frequency.|
|$e^{-at}u(t)$|$\frac{1}{a+j\omega}$|Direct integration of $\int_{0}^{\infty} e^{-t(a+j\omega)} dt$. Evaluates to $0 - (\frac{-1}{a+j\omega})$.|
|$e^{-a\vert t\vert}$|$\frac{2a}{a^2+\omega^2}$|Split into causal $e^{-at}u(t)$ and anti-causal $e^{at}u(-t)$. Sum their transforms: $\frac{1}{a+j\omega} + \frac{1}{a-j\omega}$.|
|$\text{sgn}(t)$|$\frac{2}{j\omega}$|Treat as the limit of $e^{-at}u(t) - e^{at}u(-t)$ as $a \to 0$. $\frac{1}{j\omega} - \frac{1}{-j\omega}$.|
|$u(t)$|$\pi\delta(\omega) + \frac{1}{j\omega}$|Relate to $\text{sgn}(t)$: $u(t) = \frac{1}{2} + \frac{1}{2}\text{sgn}(t)$. Transform of $1/2$ gives the $\delta$, transform of $\text{sgn}/2$ gives the $1/j\omega$.|
|$\text{rect}(t/\tau)$|$\tau \text{sinc}(\frac{\omega \tau}{2\pi})$|Direct integration of $1$ from $-\tau/2$ to $\tau/2$. Yields $\frac{2}{\omega}\sin(\omega\tau/2)$, which shapes into $\text{sinc}$.|
|$\text{sinc}(Wt/\pi)$|$\text{rect}(\omega/2W)$|**Duality:** Since a box in time is a sinc in frequency, a sinc in time is a sharp box (ideal lowpass filter) in frequency.|
|$\cos(\omega_0 t)$|$\pi[\delta(\omega-\omega_0) + \delta(\omega+\omega_0)]$|**Euler's:** $\frac{1}{2}(e^{j\omega_0 t} + e^{-j\omega_0 t})$. A complex exponential is just a shifted delta in frequency.|
|$\sin(\omega_0 t)$|$\frac{\pi}{j}[\delta(\omega-\omega_0) - \delta(\omega+\omega_0)]$|**Euler's:** $\frac{1}{2j}(e^{j\omega_0 t} - e^{-j\omega_0 t})$. Same as cosine but with the imaginary scaling and phase inversion.|
|$\sum_{n} \delta(t-nT)$|$\frac{2\pi}{T}\sum_{k} \delta(\omega - \frac{2\pi k}{T})$|Finding the Fourier Series of the impulse train yields coefficients $a_k = 1/T$. The transform is those coefficients as impulses.|
|$e^{-at^2}$|$\sqrt{\frac{\pi}{a}} e^{-\omega^2/4a}$|**Self-duality:** Complete the square in the exponent of the Fourier integral. A Gaussian in time is always a Gaussian in frequency.|

### Core DTFT Pairs

|**Signal x[n]**|**Transform X(ejω)**|**Quick Derivation / Mental Trigger**|
|---|---|---|
|$\delta[n]$|$1$|Direct sum: $\sum \delta[n] e^{-j\omega n}$ only exists at $n=0$, which is $e^0 = 1$.|
|$a^n u[n]$|$\frac{1}{1-ae^{-j\omega}}$|Infinite geometric series: $\sum_{n=0}^{\infty} (ae^{-j\omega})^n$. Converges using $\frac{1}{1-r}$ where $r = ae^{-j\omega}$.|
|$1$|$2\pi\sum_k\delta(\omega-2\pi k)$|DTFT is always $2\pi$-periodic. A constant in time is an impulse at DC ($\omega=0$) that repeats every $2\pi$.|

### The "Cheat Code" Properties

|**Property**|**Formula**|**Why it makes sense (Intuition)**|
|---|---|---|
|**Time Shift**|$x(t-t_0) \leftrightarrow e^{-j\omega t_0}X(j\omega)$|Delaying a signal doesn't change its frequencies, it just adds a linear phase shift (spinning the phase angle proportional to frequency).|
|**Frequency Shift**|$e^{j\omega_0 t}x(t) \leftrightarrow X(j(\omega-\omega_0))$|Multiplying by a carrier wave translates the entire spectrum up to that carrier frequency (the basis of all radio modulation).|
|**Time Scaling**|$x(at) \leftrightarrow \frac{1}{\vert a \vert}X(j\frac{\omega}{a})$|Compressing a signal in time ($a>1$) requires faster transitions, which stretches the signal out in frequency.|
|**Time Differentiation**|$\frac{dx(t)}{dt} \leftrightarrow j\omega X(j\omega)$|Taking a derivative highlights fast changes. Multiplying by $\omega$ acts as a high-pass filter, amplifying high frequencies.|
|**Freq Differentiation**|$t x(t) \leftrightarrow j \frac{dX(j\omega)}{d\omega}$|The dual of time differentiation. Multiplying by $t$ ramps up the signal over time, causing sharper variations in the spectrum. (Crucial for deriving $t e^{-at}u(t)$).|
|**Convolution**|$x(t) * y(t) \leftrightarrow X(j\omega)Y(j\omega)$|Filtering a signal in time is just scaling its individual frequency components up or down.|
|**Multiplication**|$x(t)y(t) \leftrightarrow \frac{1}{2\pi}X(j\omega) * Y(j\omega)$|Windowing a signal in time blurs it in frequency (convolving with the window's spectrum).|

### 2D Digital Image Processing: Core Functional Pairs

|**Spatial Domain f(x,y)**|**Frequency Domain F(u,v)**|**DIP Application / Mental Trigger**|
|---|---|---|
|$\delta(x,y)$|$1$|A 2D spatial impulse contains every single spatial frequency equally.|
|$1$|$MN \delta(u,v)$|A perfectly flat, featureless image is a massive DC component at the origin.|
|$\cos(2\pi(u_0 x/M + v_0 y/N))$|$\frac{MN}{2} [\delta(u-u_0, v-v_0) + \delta(u+u_0, v+v_0)]$|Euler's formula in 2D. A spatial sine wave is two symmetric impulses in frequency.|
|$\text{rect}(x/X, y/Y)$|$XY\text{sinc}(uX)\text{sinc}(vY)$|**Ideal Filtering.** A sharp rectangular cutoff in frequency creates a 2D sinc in space. This causes visible "ringing" (ripple artifacts) around edges.|
|$\text{sinc}(x/X)\text{sinc}(y/Y)$|$XY\text{rect}(uX, vY)$|**Duality.** A sinc function in the spatial domain translates to a perfect rectangular window in the frequency domain.|
|$e^{-(x^2+y^2)/2\sigma^2}$|$2\pi\sigma^2 e^{-2\pi^2\sigma^2(u^2+v^2)}$|**Gaussian Blurring.** A Gaussian is perfectly smooth and positive in both domains. Smooth in frequency means smooth in space—Gaussian filters never cause ringing.|
|$\sum_m \sum_n \delta(x-m\Delta x, y-n\Delta y)$|$\frac{1}{\Delta x \Delta y} \sum_m \sum_n \delta(u-\frac{m}{\Delta x}, v-\frac{n}{\Delta y})$|**2D Sampling.** Multiplying an image by this grid in space creates infinite periodic copies of its spectrum in frequency. Aliasing occurs when these copies overlap.|

### 2D DFT Properties & Manipulation

|**Property**|**Spatial Domain**|**Frequency Domain**|**What it means / Why you care**|
|---|---|---|---|
|**Spatial Shift**|$f(x-x_0, y-y_0)$|$F(u,v) e^{-j2\pi(u x_0/M + v y_0/N)}$|Panning the image only shifts the phase. The magnitude spectrum remains identical.|
|**Frequency Shift**|$f(x,y) e^{j2\pi(u_0 x/M + v_0 y/N)}$|$F(u-u_0, v-v_0)$|Multiplying by a 2D complex exponential shifts the entire frequency spectrum.|
|**Centering the FFT**|$f(x,y)(-1)^{x+y}$|$F(u-M/2, v-N/2)$|Multiplying the image by a +1/-1 checkerboard shifts the DC component to the dead center of the frequency plot for visualization.|
|**Rotation**|$f(r, \theta+\theta_0)$|$F(\omega, \phi+\theta_0)$|Rotating an image in the spatial domain rotates its frequency spectrum by the exact same angle.|
|**2D Convolution**|$f(x,y) * h(x,y)$|$F(u,v)H(u,v)$|**Filtering.** Convolving a spatial filter mask is mathematically identical to multiplying their frequency representations.|
|**2D Multiplication**|$f(x,y)h(x,y)$|$\frac{1}{MN} F(u,v) * H(u,v)$|**Windowing.** Multiplying an image by a window in space convolves (blurs) its frequency spectrum.|
|**Separability**|$f(x)f(y)$|$F(u)F(v)$|**Optimization.** If a 2D function can be split into 1D x and y components, you can perform two fast 1D operations instead of one massive 2D operation.|

### 2D Differential Properties (Edge Detection)

|**Spatial Domain**|**Frequency Domain**|**DIP Application / Mental Trigger**|
|---|---|---|
|$\frac{\partial}{\partial x}f(x,y)$|$j2\pi u F(u,v)$|**Directional Edges.** A derivative in the x-direction highlights vertical edges. Acts as a highpass filter along the $u$-axis, zeroing out the DC component.|
|$\frac{\partial}{\partial y}f(x,y)$|$j2\pi v F(u,v)$|**Directional Edges.** Same as above, but isolates horizontal edges by filtering along the $v$-axis.|
|$\nabla^2 f(x,y)$|$-4\pi^2(u^2+v^2)F(u,v)$|**The Laplacian.** Isotropic (directionless) edge detection. The frequency multiplier $(u^2+v^2)$ is a parabolic highpass filter that amplifies fine detail and crushes flat areas.|

### Image Transforms (Matrix-Based & Fourier-Related)

|**Transform**|**Basis Functions**|**Engineering Use / Mental Trigger**|
|---|---|---|
|**Discrete Cosine (DCT)**|Cosines of varying frequencies|**Energy compaction.** Pushes almost all visual information into the top-left (low frequency) coefficients. The foundation of JPEG compression.|
|**Walsh-Hadamard (WHT)**|Square waves (values +1, -1)|**Hardware speed.** Uses zero sines or cosines, just additions and subtractions. Extremely cheap to compute on raw hardware.|
|**Haar Transform**|Step functions (local +1, -1, 0)|**Edge isolation.** Analyzes local features rather than global ones. Ideal for detecting sudden, sharp transitions in an image.|
|**Slant Transform**|Sawtooth / discrete linear ramps|**Gradient compression.** Designed to compress images with smooth, linear changes in brightness (e.g., fading skies, smooth shadows).|
|**Wavelet (DWT)**|Scaled/shifted "mother wavelets"|**Time-frequency localization.** The Fourier transform reveals _what_ frequencies exist; wavelets tell you exactly _where_ in the image they exist (used in JPEG2000).|