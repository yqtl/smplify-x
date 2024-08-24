Yes, in the context of Blender and many 3D modeling and animation systems, the head of a bone can be considered as the "joint location." The head of a bone represents the starting point or the "joint" where it connects to its parent bone. Therefore, if you're interested in the positions of joints within an armature, looking at the bone heads is a straightforward approach.

### How to Access Bone Head Positions in Blender

To read a bone's head position in Blender using Python, you can access the armature's pose bones or edit bones, depending on whether you're working in Pose Mode or Edit Mode, respectively. Here's how you can do it for both cases:

#### In Pose Mode
Pose Mode is typically used for animating bones. The bone's head position in this mode is relative to the armature's origin, taking into account the transformations applied to the bone (translation, rotation, scale).

```python
armature = bpy.data.objects['SMPLX-male']  # Replace 'YourArmatureName' with the actual name of your armature
for bone in armature.pose.bones:
    print(f"Bone: {bone.name}, Head Position in Pose Mode: {bone.head}")
```

#### In Edit Mode
Edit Mode allows you to modify the bones' rest positions, which are their default positions without any pose applied. The positions here are also relative to the armature's origin but do not reflect any pose transformations.

```python
armature = bpy.data.objects['YourArmatureName']  # Replace 'YourArmatureName' with the actual name of your armature
bpy.context.view_layer.objects.active = armature  # Set the armature as the active object
bpy.ops.object.mode_set(mode='EDIT')  # Switch to Edit Mode

for bone in armature.data.edit_bones:
    print(f"Bone: {bone.name}, Head Position in Edit Mode: {bone.head}")
```

#### World Space Position
Both of the above will give you the position relative to the armature's origin. If you need the bone's head position in world space, you need to multiply the position by the armature's world matrix:

```python
armature = bpy.data.objects['SMPLX-male']
for bone in armature.pose.bones:
    head_world_position = armature.matrix_world @ bone.head
    print(f"Bone: {bone.name}, Head Position in World Space: {head_world_position*1000}")
```

#### Revision
armature = bpy.data.objects['SMPLX-male']  # Replace 'SMPLX-male' with the actual name of your armature

# Make sure the armature is the active object
bpy.context.view_layer.objects.active = armature

for bone in armature.pose.bones:
    # Transform the local bone head position to world space using the bone's matrix
    head_world_position = armature.matrix_world @ bone.matrix @ bone.head
    print(f"Bone: {bone.name}, Head Position in World Space: {head_world_position}")

### Conclusion
Reading a bone's head position in Blender gives you the location of the joint in the context of the armature. Depending on your specific needs (e.g., during animation, rigging, or scripting), you might choose to access this information in Pose Mode or Edit Mode, and you might also need to consider whether to work in local space (relative to the armature) or world space.

## To plot bone as sphere in Blender
```
import bpy

armature = bpy.data.objects['SMPLX-male']  # Replace with your armature's name
bpy.ops.object.select_all(action='DESELECT')  # Deselect all objects

for bone in armature.pose.bones:
    # Create a mesh sphere at the bone's head position
    bpy.ops.mesh.primitive_uv_sphere_add(radius=0.02, location=armature.matrix_world @ bone.head)
    sphere = bpy.context.object  # Get the newly created sphere
    sphere.name = f"Joint_{bone.name}"  # Rename the sphere to match the bone name
    sphere.show_in_front = True  # Make the sphere draw in front of other objects
```