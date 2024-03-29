# pypcd4

## Description

pypcd4 is a modern reimagining of the original [pypcd](https://github.com/dimatura/pypcd) library,
offering enhanced capabilities and performance for working with Point Cloud Data (PCD) files.

This library builds upon the foundation laid by the original pypcd while incorporating modern
Python3 syntax and methodologies to provide a more efficient and user-friendly experience.

## Installation

To get started with `pypcd4`, install it using pip:

```shell
pip install pypcd4
```

## Usage

Let’s walk through some examples of how you can use pypcd4:

### Getting Started

First, import the PointCloud class from pypcd4:

```python
from pypcd4 import PointCloud
```

### Working with .pcd Files

If you have a .pcd file, you can read it into a PointCloud object:

```python
pc: PointCloud = PointCloud.from_path("point_cloud.pcd")

pc.fields
# ('x', 'y', 'z', 'intensity')
```

### Converting Between PointCloud and NumPy Array

You can convert a PointCloud to a NumPy array:

```python
array: np.ndarray = pc.numpy()

array.shape
# (1000, 4)
```

You can also specify the fields you want to include in the conversion:

```python
array: np.ndarray = pc.numpy(("x", "y", "z"))

array.shape
# (1000, 3)
```

And you can convert a NumPy array back to a PointCloud.
The method you use depends on the fields in your array:

```python
# If the array has x, y, z, and intensity fields,
pc = PointCloud.from_xyzi_points(array)

# Or if the array has x, y, z, and label fields,
pc = PointCloud.from_xyzl_points(array, label_type=np.uint32)
```

#### Creating Custom Conversion Methods

If you can’t find your preferred point type in the pre-defined conversion methods,
you can create your own:

```python
fields = ("x", "y", "z", "intensity", "new_field")
types = (np.float32, np.float32, np.float32, np.float32, np.float64)

pc = PointCloud.from_points(array, fields, types)
```

### Working with ROS PointCloud2 Messages

As of v0.4.0, you can convert a ROS PointCloud2 Message to a PointCloud:

```python
def callback(msg):
    pc = PointCloud.from_msg(msg)

    pc.fields
    # ("x", "y", "z", "intensity", "ring", "time")
```

### Concatenating Two PointClouds

The `pypcd4` supports concatenating two `PointCloud` objects together using the `+` operator.
This can be very useful when you want to merge two point clouds into one.

Here's how you can use it:

```python
pc1: PointCloud = PointCloud.from_path("xyzi1.pcd")
pc2: PointCloud = PointCloud.from_path("xyzi2.pcd")

# Concatenate two PointClouds
pc3: PointCloud = pc1 + pc2
```

Please note that the two PointCloud objects must have the same fields and types. If they don’t, a `ValueError` will be raised.

### Filtering a PointCloud

The `pypcd4` library provides a convenient way to filter a `PointCloud` using a subscript.

#### Using a Slice

You can use a slice to access a range of points in the point cloud. Here’s an example:

```python
# Create a point cloud with random points
pc = PointCloud.from_xyz_points(np.random.rand(10, 3))

# Access points using a slice
subset = pc[3:8]
```

In this case, subset will be a new PointCloud object containing only the points from index 3 to 7.

#### Using a Boolean Mask

You can use a boolean mask to access points that satisfy certain conditions. Here’s an example:

```python
# Create a point cloud with random points
pc = PointCloud.from_xyz_points(np.random.rand(10000, 3))

# Create a boolean mask
mask = (pc.pc_data["x"] > 0.5) & (pc.pc_data["y"] < 0.5)

# Access points using the mask
subset = pc[mask]
```

In this case, subset will be a new PointCloud object containing only the points where the x-coordinate is greater than 0.5 and the y-coordinate is less than 0.5.

#### Using Field Names

You can use a field name or a sequence of field names to access specific fields in the point cloud. Here’s an example:

```python
# Create a point cloud with random points
pc = PointCloud.from_xyz_points(np.random.rand(100, 3))

# Access specific fields
subset = pc[("x", "y")]
```

In this case, subset will be a new PointCloud object containing only the x and y coordinates of the points. The z-coordinate will not be included.

### Saving Your Work

Finally, you can save your PointCloud as a .pcd file:

```python
pc.save("nice_point_cloud.pcd")
```

## License

The library was rewritten and does not borrow any code from the original [pypcd](https://github.com/dimatura/pypcd) library.
Since it was heavily inspired by the original author's work, we extend his original BSD 3-Clause License and include his Copyright notice.
