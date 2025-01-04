---
marp: true
theme: default
math: mathjax
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;
  }
  .small-text {
    font-size: 0.75em;
  }
  .center {
    text-align: center;
  }
  section {
    background-color: #ffffff;
  }
  h1 {
    color: #234876;
  }
  .theorem {
    background-color: #f0f7ff;
    padding: 1em;
    margin: 1em 0;
    border-left: 3px solid #234876;
  }
paginate: true
backgroundColor: #fff
---

<!-- _class: lead -->
# Modelos de Difusión
## From Theory to Implementation

Eugenio Herrera-Berg
Centro Nacional de Inteligencia Artificial

---

# Overview

1. Introduction to Diffusion Models
2. Mathematical Foundation
3. Implementation Details
4. Results and Applications

---

# What are Diffusion Models?

<div class="columns">
<div>

- Generative models based on reversing a diffusion process
- Learn to denoise data iteratively
- Applications:
  - Image generation
  - Audio synthesis
  - 3D modeling

</div>
<div>

<!-- Replace with your video -->
<video controls width="100%">
  <source src="./assets/intro_animation.mp4" type="video/mp4">
</video>

</div>
</div>

---

# Mathematical Foundation

<div class="theorem">

The forward diffusion process can be described as:

$q(x_t|x_{t-1}) = \mathcal{N}(x_t; \sqrt{1-\beta_t}x_{t-1}, \beta_t\mathbf{I})$

where:
- $x_t$ is the noisy image at timestep $t$
- $\beta_t$ is the noise schedule
- $\mathcal{N}$ denotes the normal distribution

</div>

<!-- Replace with your demonstration video -->
<video controls width="60%" style="display: block; margin: auto;">
  <source src="./assets/noise_process.mp4" type="video/mp4">
</video>

---

# Forward Process Equations

The complete forward process can be written as:

$$q(x_{1:T}|x_0) = \prod_{t=1}^T q(x_t|x_{t-1})$$

With reparameterization:

$$x_t = \sqrt{\alpha_t}x_0 + \sqrt{1-\alpha_t}\epsilon$$

where $\epsilon \sim \mathcal{N}(0, \mathbf{I})$

---

# Implementation Details

```python
class DiffusionModel(nn.Module):
    def __init__(self, device):
        super().__init__()
        self.device = device
        self.network = UNet(
            dim=64,
            channels=3,
            dim_mults=(1, 2, 4, 8)
        )

    def forward(self, x, t):
        return self.network(x, t)
```

---

# Training Process Visualization

<div class="center">
<!-- Replace with your training visualization -->
<video controls width="80%">
  <source src="./assets/training.mp4" type="video/mp4">
</video>
</div>

---

# Results: Image Generation

<div class="columns">
<div>

Generated samples at different timesteps:
- Initial noise
- Intermediate steps
- Final output

</div>
<div>

<!-- Replace with your generation video -->
<video controls width="100%">
  <source src="./assets/generation.mp4" type="video/mp4">
</video>

</div>
</div>

---

# Comparison with Other Models

| Model | FID Score | Training Time | GPU Memory |
|-------|-----------|---------------|------------|
| DDPM | 3.21 | 24h | 16GB |
| DDIM | 4.16 | 12h | 12GB |
| Ours | 3.45 | 18h | 14GB |

---

<!-- _class: lead -->
# Thank You!

## Questions?

GitHub: your-username
Email: your.email@example.com

---