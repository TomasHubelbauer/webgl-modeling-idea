# WebGL Modeling Idea

This repository describes an idea I have for building 3D models in WebGL. It's an API idea, I don't yet know if there is a feasible
implementation for it. I might iterate on it or not. Just putting this out here.

The general premise is that there are methods for building 3D primitives, like `buildCube`, `buildSphere`, `buildPlane` etc.
These accept parameters such as dimensions etc. and add values to the vertex and index buffers.

The vertex and index buffers get updated after each API invocation so at any point during the modeling, the mesh can be displayed.

Once the user has selected a primitive to work off of, they use the sculpting methods available on the return type of these builder
methods.

If the user chose the cube builder for example, it's return type might be an object with fields for each of the faces (front, back,
top, bottom, left, right). Additionally, each face has further accessors for it's edges and vertices.

Each of this further primitives have modeling and sculpting methods and associated related entities. For example, an edge as accessed
from one face might have a property for the other face if it is shared by two faces. Or it might have a property for the loop it is a
part of. A face (a quad) might have a helper to cut it into two tris. A tri might have a way to fuse into a quad through one of its
shared edges. There are methods for cutting, translating, rotating, extruding etc.

The resulting value from all this is an object with the vertex and index buffers and the model view matrix.

There might be multiple ways to specify an operation parameter (like percentages [relative] versus absolute values). And it may be so
that when an operation with an absolute value is applied, another operation applied might have an ambiguous path to the result. E.g.:
If an edge is split at 20 % and 80 % and then prolonged, it's not necessarily given that the middle section should always stal in the
middle. Maybe it will be possible to work around this by just not exposing a prolong method on cut edges (which may not even be a type
but instead the cut method would return three new edges) so this might be a non-issue. But if it is, maybe the op methods might return
a type which allows the user to disambiguate and then they proceed.

Some methods, like cut, might consume the input entity (the edge or the face) and return an array of multiple new entities. If such a
thing happens, it might be good to be able to invalidate the input value. JavaScript doesn't have move semantics so this is impossible
to really do.

An idea how to achieve this would be to instead of accepting the entity itself would be to accept its enclosing scope and a lambda to
extract it from it (or maybe just a textual name) and use the `delete` operator` on the scope to destroy the object so references to
it from elsewhere stop working and prevent invalid operations. I'm unsure if it will be possible to bend `delete` in such a way.

That's all I have for now.
