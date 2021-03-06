# VoxBox
The Voxel Toolbox (a.k.a 'VoxBox') is a Python package for working with voxel data, primarily for making voxel art and video games.

## Features
* Export NumPy array as [MagicaVoxel](https://ephtracy.github.io/) file.

## Installation
Download the code update your $PYTHONPATH environment variable to ensure your Python interpreter can find it. I have no current plans to make this into a proper package with PIP installer, etc.

## Example
The following snippet shows how to export a NumPy array as a MagicaVoxel file. See [waves.py](examples/waves/waves.py) for the complete code

```python
# Define the size of the volume
col_count = 126 # Width
row_count = 126 # Height
plane_count = 64 # Depth
frame_count = 30

palette = generate_rainbow_colourmap()

# Start with an empty list of volumes
volume_list = []

# Later we will choose a material based on the height (plane) of the voxel
palette_offset_per_plane = (len(palette) - 1.0) / plane_count

# For each voxel in the volume
for frame in range(0, frame_count):
    
    print("Generating frame {} of {}...".format(frame + 1, frame_count))
    
    volume = np.zeros((plane_count, row_count, col_count), dtype=np.uint8)
    
    # Create a simple heightmap (could also load something from disk)
    time_step = frame / frame_count
    heightmap = generate_waves_heightmap(col_count, row_count, time_step)

    for plane in range(0, plane_count):
        for row in range(0, row_count):
            for col in range(0, col_count):
                
                # Get the height from the heightmap, and
                # scale to the height of the volume
                height = heightmap[row][col]
                height *= plane_count
                
                # If the current voxel is below the
                # heightmap then set it to be solid.
                if plane <= height:
                    volume[plane][row][col] = palette_offset_per_plane * plane
                  
    # Add the new frame (volume) to the list
    volume_list.append(volume)
    

# Save the volume to disk as a MagicaVoxel file.
print("Saving results...")
filename = "waves.vox"
voxbox.magicavoxel.write(volume_list, filename, palette)
print("Done.")
```
    
# Result
The above code generats the following image:
![Screenshot of result](examples/waves/result.png)
