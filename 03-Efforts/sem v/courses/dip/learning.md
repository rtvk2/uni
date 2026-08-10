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
