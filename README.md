# bark-conv
Converter between the BRK format and JSON. BRK is used for animating register and constant colors in some of nintendo's GC and Wii games.
As an example, BRK sometimes controls mesh colors in Pikmin 2 for different entities that use the same BMD and in Super Mario Galaxy it 
creates a glow effect on the Shadow Mario that you race in some stages.
In more specific terms, BRK changes color values in an interpolated way over time.

# Requirements
* Python 3 

The latest version of Python can be found at https://www.python.org/downloads/ <br>
When installing it on Windows, make sure Python is added to PATH so that the included .bat files will work properly.

# Usage
## Drag & Drop
if you are on Windows, the provided brkconvert.bat file allows simply dropping a file on it to convert it.
BRK files will be converted to JSON, and JSON will be converted to BRK. 

## Command line usage
```
python ./bark-conv.py [-h] input [output]

positional arguments:
  input              Path to brk or json-formatted text file.
  output             Path to which the converted file should be written. If
                     input was a BRK, writes a json file. If input was a json
                     file, writes a BRK.If left out, output defaults to
                     <input>.json or <input>.brk.

optional arguments:
  -h, --help         show this help message and exit
```

## About the JSON structure
Header:
* loop mode: 0 and 1: plays once; 2: loops; 3: Play once forward, then backward; 4: Like 3 but on repeat
* duration: How long the animation plays in frames

Animations:
* material name: Name of the material to which the animation is applied. Name needs to match a material name from the BMD/BDL model to which the BRK belongs
* unknown: A unknown value, possibly the index of the register or constant color the animation should be applied to

BRK works with keyframes. Each entry in the red, green and blue sections is made of a group of 4 values that form a keyframe. 
First value is the frame number of the keyframe. 
Second is the color value, third and fourth are the ingoing and outgoing tangents. 
Tangents affect the interpolation between two consecutive keyframes. BRK uses Cubic Hermite Interpolation for this.
Color values should be between 0 and 255. Values above 255 are theoretically possible but untested.


## Tips
* Having trouble making animations? Take existing BRKs, change the material name of the animation to one from your model and experiment with it!
* An angle scale of 1 means you can have rotations between -180 and 180. An angle scale of 2 allows for -360 to 360.
* For a smooth change from one color value to the next you need three key frames. 
Example: Color should go from 255 to 0 over 90 frames and then from 0 to 255 over another 90 frames. That means for the outgoing tangent of
the first frame you use the value 255/90 which rounded to the nearest integer is -3. For the ingoing tangent of the first frame you use 3. 
The process for the second frame is similar but we invert the sign of the tangents because the color value is going up again.
The third frame should be the same as the first frame.
* When making custom models, usually the material names in the modelling program matches the material names in the resulting BMD. Sometimes
a suffix might be appended to the material name on conversion to BMD/BDL though (in my testing with exporting DAE in Blender, the material names received
the suffix -material). In this case the material name in the BRK needs to reflect this.

