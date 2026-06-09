# **Neuromorphic DVS \+ EMD Motion Field**

Try it live at : 

https://anttiluode.github.io/NeuromorphicDVSplusEMDfield/

*A trainless, O(K) optical flow sensor running natively in the browser. Geometric Neuron V5 meets the Hassenstein–Reichardt detector.*  
**PerceptionLab / Antti Luode**  
**Do not hype. Do not lie. Just show.**

## **What this is**

This repository contains a single-file, zero-dependency prototype (index.html) that extracts a real-time 2D motion vector field from a standard webcam. It achieves this by chaining two biological primitives:

1. **DVS (Dynamic Vision Sensor) Delta-Coding:** The camera feed is gated by a sparsity threshold. Pixels only emit an "event" (a spike) when their brightness changes significantly. Background static is ignored.  
2. **Hassenstein–Reichardt EMD (via Koopman Geometry):** Motion direction is calculated using the Elementary Motion Detector circuit discovered in fly vision in 1956, derived here from the Koopman angular momentum of the field:  
   L \= Im(z(t) · z\*(t-lag))

Standard dense optical flow (like Lucas-Kanade) solves polynomial equations for every pixel. This engine uses pure arithmetic, computing a bilinear cross-time product *only* on the sparse active pixels.

## **The Math**

For any active pixel, the engine calculates the horizontal (L\_x) and vertical (L\_y) motion components by multiplying the current state of a neighbor by the delayed state of the center, and subtracting the mirror:

JavaScript  
// Horizontal (X-axis) phase velocity  
L\_x \= right(t) \* center(t-lag) \- center(t) \* right(t-lag)

// Vertical (Y-axis) phase velocity  
L\_y \= down(t) \* center(t-lag) \- center(t) \* down(t-lag)

The sign of L\_x and L\_y gives the direction of the wave. The magnitude gives the speed. Because it is a bilinear product, it escapes the Wiener-Khinchin ceiling that limits magnitude-only / Fourier-power readouts. It reads the true direction of the spatial orbit over time.

## **The Honest Ledger**

**What is verified in code:**

* **Delta-Code Sparsity:** When you stop moving, the compute cost drops to near zero. The engine skips the math entirely for regions without active DVS events.  
* **Native Chirality:** The vector field accurately distinguishes left vs. right and up vs. down using only array multiplication, no model weights.

**What is an approximation:**

* **The Sensor:** This uses a standard 30fps/60fps webcam and simulates DVS polarity in software. A true physical event camera operates continuously at microsecond resolution.

**The lineage:**  
The math driving this field is the spatial application of the Geometric Neuron V5 architecture (PerceptionLab, June 2026), specifically the discovery that Koopman angular momentum acts as a native, trainless direction-of-time readout.
