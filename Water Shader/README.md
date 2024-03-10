# Water Shader

## Part I: Gerstner Wave

![Screenshot_1](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/1c7e4bf6-205d-4579-88ab-0fb57f00adcb)

This setup here is a simple Gerstner Wave that moves along a Direction vector in a sine-like pattern

![Screenshot_13](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/6a3ff8d1-4940-4723-962d-dc45ac6e2722)

This is the complete Gerstner Wave with the Absolute World Position that excludes offsets and the gravitational force set to 980m/s

![Screenshot_15](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/1fa60acb-63e1-47f4-90c7-09babad9b0eb)

To make the waves sharper, we subtract a cosine of the function from the result (eats away from the smoothness of the peaks)

![Screenshot_14](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/fde71515-3edd-470a-917d-7d43dcac9b0c)

Next up we derive Normal data from the sine and cosine function outputs

![Screenshot_18](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/7b3e0d96-1c4b-43f4-813b-f2fa7fec09f7)

Here are the Master Material values that seem to work OK. You can add a Vertex Interpolator node before the Shader's Normal input to move ~50 instructions from PS to VS

![Screenshot_17](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/37b71684-3a1b-4245-84a0-c1e0e3e94b02)

