# LearnIR: Learnable Posterior Sampling for Real-World Image Restoration

<p align="center">
  <b>International Conference on Learning Representations (ICLR 2026)</b>
</p>

<p align="center">
  Yihang Bao<sup>1†</sup>,
  Zhen Huang<sup>2†</sup>,
  Shanyan Guan<sup>2</sup>,
  Songlin Yang<sup>2</sup>,
  Yanhao Ge<sup>2</sup>,
  Wei Li<sup>2</sup>,
  Bukun Huang<sup>3</sup>,
  Zengmin Xu<sup>1,4,5*</sup>
</p>

<p align="center">
  <sup>1</sup>Guilin University of Electronic Technology &nbsp;&nbsp;
  <sup>2</sup>vivo Mobile Communication Co., Ltd &nbsp;&nbsp;
  <sup>3</sup>Zhejiang Gongshang University <br>
  <sup>4</sup>Center for Applied Mathematics of Guangxi (GUET) &nbsp;&nbsp;
  <sup>5</sup>Anview.ai
</p>

<p align="center">
  † Equal Contribution &nbsp;&nbsp;&nbsp;
  * Corresponding Author
</p>

<p align="center">
  📄 Paper (Coming Soon) • 📌 Poster • 📊 Project Page
</p>

---

# 🔥 Abstract

Image restoration in real-world conditions is highly challenging due to heterogeneous degradations such as haze, noise, shadows, and blur.

Existing diffusion-based restoration methods remain limited:

- Conditional generation struggles to balance fidelity and realism.
- Inversion-based approaches accumulate trajectory errors.
- Posterior sampling methods require a known forward degradation operator, which is rarely available in practice.

We introduce **LearnIR**, a learnable diffusion posterior sampling framework that eliminates the need for an explicit forward operator. 

LearnIR trains a lightweight correction model to directly predict gradient correction distributions, enabling **Diffusion Posterior Sampling Correction (DPSC)** that maintains posterior consistency during sampling.

Additionally, we propose a **Dynamic Resolution Module (DRM)** that dynamically adjusts resolution across diffusion timesteps, preserving global structure in early stages and refining fine textures later — without requiring a pretrained VAE.

Experiments on ISTD, O-HAZE, HazyDet, REVIDE, and our newly constructed FaceShadow dataset demonstrate state-of-the-art performance in PSNR, SSIM, and LPIPS.

---

# 🧠 Method

## Framework Overview

<p align="center">
  <img src="assets/framework.png" width="90%">
</p>

LearnIR integrates:

1. **Dynamic Resolution Module (DRM)**
2. **Diffusion Posterior Sampling Correction (DPSC)**

The framework identifies an optimal sampling step \(T'\) to bypass unstable intermediate states and maintain trajectory consistency.

---

## 1️⃣ Dynamic Resolution Module (DRM)

We introduce a time-dependent downsampling operator:

\[
\mathbf{z}_0^{(t)} = \mathcal{D}(\mathbf{x}_0, s(t)), \quad
\mathbf{z}_y^{(t)} = \mathcal{D}(\mathbf{y}, s(t))
\]

This constructs a resolution-aware latent space.

The latent residual is defined as:

\[
\mathbf{R}_\mathbf{z} = \mathbf{z}_y^{(t)} - \mathbf{z}_0^{(t)}
\]

Forward diffusion becomes:

\[
q(\mathbf{z}_t | \mathbf{z}_0^{(t)})
=
\mathcal{N}
\left(
\sqrt{\bar{\alpha}_t}\mathbf{z}_0^{(t)} +
(1-\sqrt{\bar{\alpha}_t})\mathbf{R}_\mathbf{z},
(1-\bar{\alpha}_t)\mathbf{I}
\right)
\]

### Advantages

- Early high-noise stages → low resolution → strong global modeling
- Late stages → native resolution → detailed refinement
- No additional pretrained VAE required

---

## 2️⃣ Diffusion Posterior Sampling Correction (DPSC)

Standard denoising objective:

\[
\mathcal{L}_{denoise}
=
\mathbb{E}
\|\epsilon - \epsilon_\theta(\mathbf{z}_t,t)\|_2^2
\]

However, this does not guarantee reverse posterior consistency.

We derive that posterior discrepancy follows:

\[
\mathbf{z}_{t-1}^{pred}
-
\mathbf{z}_{t-1}^{forward}
\sim
\mathcal{N}(\mu(\cdot), \sigma^2(\cdot)\mathbf{I})
\]

We learn to predict the correction mean:

\[
\mathcal{L}_{consistency}
=
\mathbb{E}
\|
\mu - \hat{\mu}_\theta
\|_2^2
\]

Final training objective:

\[
\mathcal{L}_{total}
=
\mathcal{L}_{denoise}
+
\lambda \mathcal{L}_{consistency}
\]

---

# 📊 Experiments

We evaluate LearnIR on:

- ISTD
- O-HAZE
- HazyDet
- REVIDE
- **FaceShadow (ours)**

---

## FaceShadow Dataset

We construct a real-world facial shadow dataset with:

- 1,000 test images
- Diverse identities
- Complex illumination
- Soft and hard shadow patterns
- Internet-collected natural images

---

## Visual Comparisons

<p align="center">
  <img src="assets/visual_comparison.png" width="95%">
</p>

LearnIR effectively:

- Removes complex spatial shadows
- Preserves identity-specific features
- Maintains skin texture
- Avoids structural artifacts
- Produces natural lighting transitions

---

## Real-World Generalization

<p align="center">
  <img src="assets/real_data.png" width="95%">
</p>

Our method demonstrates strong robustness and generalization ability in uncontrolled real-world conditions.

---

# ⚙️ Installation

```bash
git clone https://github.com/yourname/LearnIR.git
cd LearnIR

conda create -n learnir python=3.10
conda activate learnir
pip install -r requirements.txt
