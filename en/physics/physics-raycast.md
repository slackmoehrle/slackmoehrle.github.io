# Raycast Detection

__Raycast detection__ is a very important function and is often used to judge various situations. The essence is to make a **intersection judgment** between a ray and another shape, as shown in the figure below.

![raycast](img/raycast.jpg)

## Constructing a Ray

The `ray` is under the `geometry` namespace of the `cc` module, so in order to access to `ray`, we need to import `geometry`:

```ts
import { geometry } from "cc";
```

![import geometry](img/import-geometry.jpg)

The `ray` is composed of **start point** and **direction**. There are the following common methods to construct a ray:

1. Via `start point++direction`, such as `ray` constructor or static interface `create`:

```ts
import { geometry } from "cc";
const { ray } = geometry;
// Construct a ray starting from (0, -1, 0) and pointing to the Y axis
// The first three parameters are the starting point, the last three parameters are the direction
const outRay = new ray(0, -1, 0, 0, 1, 0);

// Or through the static method create
const outRay2 = ray.create(0, -1, 0, 0, 1, 0);
```

2. Via `start point` +` another point on the ray`, for example the static interface `fromPoints` in the `ray`:

```ts
import { geometry, Vec3 } from "cc";
// Construct a ray starting from the origin and pointing to the Z axis
const outRay = new geometry.ray();
geometry.ray.fromPoints(outRay, Vec3.ZERO, Vec3.UNIT_Z);
```

3. Use the camera to construct a ray emitted from the origin of the camera to a point on the screen (or the near plane of the camera):

> **Note**: First you need to get a reference to a camera component or camera instance.

> **Note**: The order of the interface parameters exposed by both the camera component and the camera instance is not the same.

```ts
import { geometry, CameraComponent } from "cc";
const { ray } = geometry;
// It is assumed here that there is already a reference to cameraCom
const cameraCom: CameraComponent;
const cameraCom: CameraComponent;
// Get a ray emitted by the screen coordinates (0, 0)
const outRay = new ray();
cameraCom.screenPointToRay(0, 0, outRay);
```

## Interface Introduction

__Cocos Creator 3D__ provides a set of ray detection functions based on the physics engine in the `v1.0.1` version.

However, it should be noted that the detected object is a physics collider, and the corresponding collider component on the inspector panel, such as `BoxColliderComponent`.

Currently, the interface is provided by PhysicsSystem, which has the following two categories:

- `raycastAll` : Detect all colliders and return a Boolean value to indicate whether the detection was successful.
- `raycastClosest` : Detect all colliders and return Boolean value as well.

Parameter explanation:

- `worldRay` : Rays in world space
- `mask` : Mask for filtering, you can pass in the packets to be detected
- `maxDistance` : Maximum detection distance, please do not pass Infinity or Number.MAX_VALUE
- `queryTrigger` : Whether to detect triggers

## Getting Results

To get the detection results of the above interfaces, you need to use the following methods separately:

- Get the detection results of `raycastAll`: `PhysicsSystem.instance.raycastResults`
- Get the detection result of `raycastClosest`: `PhysicsSystem.instance.raycastClosestResult`

> **Note**: The returned object is read-only and reused, and the corresponding result will be updated after each call to the detection interface.

## Information Stored By Results

The information is stored by `PhysicsRayResult`, which mainly has the following information:

- `collider` : Collider that is hit
- `distance` : The distance between the hit point and the starting point of the ray
- `hitPoint` : hit point (in world coordinate system)
- `hitNormal` :The normal of the hit point's face (in the world coordinate system) (supported in `v1.1` version, currently there is no such information in `builtin`)

Related test cases can be found [here](https://github.com/cocos-creator/test-cases-3d/blob/master/assets/cases/physics/scenes/physics-raycast.scene).

---

Return to the [using physics](physics-use.md) documentation.
