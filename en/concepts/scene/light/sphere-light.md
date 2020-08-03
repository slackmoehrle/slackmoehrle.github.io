# Spherical Lighting

__Cocos Creator 3D__ uses spherical light instead of **point lighting**, because physical light sources in the real world have light source size attributes.

![sphere light](sphere-light.jpg)

| Parameter | Description |
|:-------:|:---:|
| Color | Light source color |
| UseColorTemperature | Whether to enable color temperature |
| ColorTemperature | Color temperature |
| Size | Light source size |
| Range | Lighting impact range |
| Term | Selected unit for light intensity <br> Spherical light supports two unit system: **luminous power** and **luminance** |
| LuminousPower | Luminous power in **lumens (*lm*)** <br> When __Term__ is specified as __LUMINOUS_POWER__, lumen is used to indicate the light intensity |
| Luminance | Brightness, unit **Candela per square meter (*cd/m<sup>2</ sup>*)** <br>When __Term__ is specified as __LUMINANCE__, brightness is used to indicate light intensity |

---

Continue to the [Spotlight](spot-light.md) documentation.
