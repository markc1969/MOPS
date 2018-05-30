MOPs
======

![MOPs logo](https://https://github.com/toadstorm/MOPS/mops_logo_01.png)

Motion OPerators for Houdini, a motion graphics toolkit.

MOPs is intended to be an easy way to manipulate lots of copies of things, leveraging Houdini's packed primitives. The goal is a familiar and fast workflow for motion graphics artists migrating from other platforms, as well as a powerful toolkit for more experienced Houdini artists to quickly design and execute new effects.

MOPs is based on an internal framework of nodes that convert point attributes to packed primitive intrinsic attributes and back again, making it easy for technical artists to develop new MOPs modifiers.

### Installation:

Navigate to the folder you want to contain MOPs, and from BASH / Git BASH type:
`git clone https://github.com/toadstorm/MOPS.git`

Edit your houdini.env file and create a variable called MOPS that points to the new folder:
`MOPS = "/path/to/MOPS"`

Finally, add `$MOPS/otls` to your HOUDINI_OTLSCAN_PATH:
`HOUDINI_OTLSCAN_PATH = $MOPS/otls;@/otls`

### Usage basics:

The main types of nodes in MOPs are the Generators, Modifiers, and Falloff nodes. Generators like the MOPs Instancer create copies of objects. Modifiers transform or otherwise change the objects. Falloffs weight the effects of Modifiers.

The simplest network to start with is the MOPs Instancer. Create a Instancer and an empty File SOP, then add the File SOP as an Instance Object under the MOPS Instancer > Instances tab. The Instancer will create several copies of the File object. If you want to copy onto another mesh, you can change the Distribution Type to Mesh and select an Input Mesh to copy to. If you add multiple Instance objects to the Instancer, by default they will be randomly selected for each instance. You can use the MOPs Index From Attribute node to determine exactly which objects will be instanced where.

Append a MOPs Transform Modifier to the MOPs Instancer. Try playing with the rotation and translation settings. Next, connect a MOPs Shape Falloff in between the MOPs Instancer and the Transform Modifier. The Falloff node by default will change how much the Transform Modifier affects the objects upstream. If you want to reposition the center of the falloff effect, connect a MOPs Transform Falloff node to the second input of the Shape Falloff. All MOPs Modifiers by default will respect the Falloff value assigned to incoming points. 

For more detailed examples, see the "examples" folder for HIP files.

### Developers:
This section is in progress.

MOPs is essentially a handy front-end for manipulating the transformations of Houdini packed primitives. The main node that handles these transformations is the MOPs Apply Attributes SOP. This SOP takes input packed primitives and a matching set of input points with typical instancing attributes, such as p@orient, v@scale, etc. and uses those attributes to modify the primitive intrinsics of the packed primitives in predictable ways. The MOPs Extract Attributes SOP can operate in the other direction, taking the primitive intrinsics and generating instancing point attributes from them. The Falloff nodes create a point attribute called @mops_falloff on the points, which MOPs Apply Attributes will use to weight the applied effects.

Reserved point attributes include:
* f@mops_falloff: the falloff value generated by Falloff nodes. A value of 0 implies that no effect will take place.
* i@mops_index: The index attribute used to decide what object is cloned to what point, if multiple objects are connected to the MOPs Instancer multi-input.
* p@mops_orient: An orientation offset to allow for changes of the local rotation frames without actually rotating the object.
* v@euler: Created internally by the Transform Modifier. If detected, the Apply Attributes SOP will apply these rotations to the incoming points instead of using p@orient. This is to prevent flipping when animating rotations beyond 180 degrees on a given axis.

MOPs is developed and maintained by Moritz Schwind and Henry Foster. Additional contributions by Adam Swaab, Jake Rice, Ian Farnsworth, and Kevin Weber. 
