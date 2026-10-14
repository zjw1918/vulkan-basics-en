# Vulkan Basics · Companion Code

The code for every episode and every step of the video series "Vulkan Basics". It follows [vulkan-tutorial.com](https://vulkan-tutorial.com): starting from an empty window, step by step to a triangle, a spinning square, textures, the Viking room model, mipmaps and multisampling, and finally 8,000 particles pushed by a compute shader.

**Every step is a commit with a tag, and every one of them builds and runs.** The tag in the top-right corner of the video, like `ep03.2`, is a tag here:

```bash
git checkout ep03.2        # episode 3, step 2
git checkout ep05          # the code at the end of episode 5
git diff ep03.1 ep03.2     # what this step changed
```

## Build and run

You need a C++17 compiler, CMake ≥ 3.20, the Vulkan SDK (headers, loader, validation layers, glslc), GLFW and GLM.

| OS | Install |
|---|---|
| Windows | [LunarG Vulkan SDK](https://vulkan.lunarg.com/); GLFW and GLM via vcpkg |
| Ubuntu 24.04 | `sudo apt install build-essential cmake vulkan-tools libvulkan-dev vulkan-validationlayers spirv-tools glslc libglfw3-dev libglm-dev` (without a GPU driver, add `mesa-vulkan-drivers` for the lavapipe CPU renderer) |
| macOS | `brew install molten-vk vulkan-loader vulkan-headers vulkan-validationlayers shaderc glfw glm cmake` (or the LunarG SDK) |

```bash
cmake -S . -B build
cmake --build build
cd build && ./vulkan_app       # episode 12 also builds ./particles
```

The programs read `shaders/`, `textures/` and `models/` by relative path (CMake copies them into `build/`), so run them from `build/`. The default is a Debug build with the validation layers on; a Release build (`-DCMAKE_BUILD_TYPE=Release`) turns them off.

**Tested on:** macOS (Apple M4 + MoltenVK, where the videos were recorded); Ubuntu 24.04 (Mesa lavapipe in a container, every tag builds and runs). Windows is untested.

**macOS:** Apple platforms run Vulkan through MoltenVK. The lines wrapped in `#ifdef __APPLE__` (`VK_KHR_portability_enumeration`, `VK_KHR_get_physical_device_properties2`, `VK_KHR_portability_subset`) are there for it; on Windows and Linux they are not compiled.

## Episodes and tags

| Ep | Content | Tags |
|---|---|---|
| 1 | Grand Opening: window, instance and validation layers | ep01.1 – ep01.4 |
| 2 | Finding the GPU: physical devices, queue families, logical device, window surface | ep02.1 – ep02.4 |
| 3 | A Stack of Plates: swap chain and image views | ep03.1 – ep03.5 |
| 4 | Building the Line: shaders and the graphics pipeline | ep04.1 – ep04.5 |
| 5 | The First Triangle: framebuffers, commands and sync | ep05.1 – ep05.4 |
| 6 | Running Smoothly: frames in flight and swap chain recreation | ep06.1 – ep06.2 |
| 7 | Bring Your Own Vertices: vertex, staging and index buffers | ep07.1 – ep07.4 |
| 8 | Make It Spin: uniform buffers and descriptors | ep08.1 – ep08.2 |
| 9 | Add a Picture: textures and samplers | ep09.1 – ep09.3 |
| 10 | Into 3D: depth buffer and model loading | ep10.1 – ep10.4 |
| 11 | Polish: mipmaps and multisampling | ep11.1 – ep11.2 |
| 12 | More Than Drawing: compute shader particles (`src/particles.cpp`) | ep12.1 – ep12.3 |

`git log --oneline` shows what each step does.

### Deliberate breakage

Three tags make the validation layer report an error when they run. That is part of the "let's break it" moments in the videos, and the next step fixes it:

- `ep05.3`: one "render finished" semaphore per frame, as in the official code; recent validation layers report `VUID-vkQueueSubmit-pSignalSemaphores-00067`. `ep05.4` uses one per swap chain image.
- `ep07.1`: the pipeline describes vertex input, but no vertex buffer is bound yet (`VUID-vkCmdDraw-None-04007`); `ep07.2` adds it.
- `ep08.1`: the pipeline uses a descriptor set that is not bound yet (`ep08.2` adds it).

`ep10.1` draws in the wrong order (the far rectangle covers the near one); `ep10.2` fixes it with a depth buffer.

## Differences from the official code

1. The macOS portability extensions (see above).
2. "Render finished" semaphores are allocated per swap chain image, not per frame (see `ep05.3` / `ep05.4`). The official code triggers the error above with recent validation layers.
3. Each step prints a few more lines (GPU name, queue families, swap chain parameters, model vertex count…), so you can compare with the video.
4. The particles start from a fixed random seed, so every run is the same.

This repository is the English edition: same tags, same code, English comments. The Chinese edition is [zjw1918/vulkan-basics](https://github.com/zjw1918/vulkan-basics).

## Third-party content

- `third_party/stb_image.h`: stb_image v2.30, public domain / MIT (see the file).
- `third_party/tiny_obj_loader.h`: tinyobjloader, MIT.
- `models/viking_room.obj`, `textures/viking_room.png`: [Viking room](https://sketchfab.com/3d-models/viking-room-a49f1b8e4f5c4ecf9e1fe7d81915ad38) by nigelgoh, CC BY 4.0 (the resized and reoriented version used by the official tutorial).
- `textures/texture.jpg`: the CC0 image used by the official tutorial (pixabay).
- The program structure and most of the code come from the official tutorial's code listings (CC0 1.0).

## License

The code written for this repository is dedicated to the public domain under [CC0 1.0](LICENSE): use it freely, no attribution needed (the same as the official tutorial's code listings). The third-party files listed above keep their own licenses.
