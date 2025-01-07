---
marp: true
theme: default
math: mathjax
style: |
  section {
    background-color: #ffffff;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    padding-top: 1em;
  }
  .content {
    display: flex;
    flex-direction: column;
    justify-content: center;
    flex: 1;
    width: 100%;
    height: 100%;
  }
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
  h1 {
    color: #234876;
  }
  .theorem {
    background-color: #f0f7ff;
    padding: 0.5em;
    margin: 1em 0;
    border-left: 3px solid #234876;
  }
  .image-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 1em 0;
  }
  .citation {
    font-size: 0.7em;
    color: #666;
    margin-top: 0.5em;
    text-align: center;
    max-width: 80%;
  }
paginate: true
---

<div class="content">

# Modelos de Difusión
## Diplomado Universidad de Tarapacá

Eugenio Herrera-Berg
Centro Nacional de Inteligencia Artificial

</div>

---

# ¿Qué es un modelo generativo?

<div class="content center">

Es un modelo que busca aprender a representar una distribución.

</div>

---

# ¿Qué es un modelo generativo?

Es un modelo que busca aprender a representar una distribución.

<div class="theorem">
Es decir, dado:

- Dataset $X = \{x_{1}, x_{2}, ..., x_{n}\}$, cuya distribución instrínseca desconocida es $p\_data(x)$
- Una distribución paramétrica $p\_{\theta}(x)$

Buscamos encontrar los parámetros $\theta^{*}$ tales que:

$\theta^{*} = min \ D(p\_data \ || \  p\_{\theta})$

Donde $D$ representa alguna métrica de distancia o divergencia entre distribuciones.
</div>

---

# ¿Qué es un modelo generativo?

<div class="content">

![alt text](image.png)

</div>

---

# Ejemplos de modelos generativos

<div class="content">

- GPTs
- **Generative Adversarial Networks**
- Variational Autoencoders

</div>

---

# Generative Adversarial Networks

<div class="content">

![alt text](assets/images/002.png)

</div>

---

# Fortalezas GANs

<div class="columns">

<div class="content">

- Generación de datos sintéticos de alta calidad.
- Rápida generación de imágenes.
</div>
<div class="center">

![height:500](assets/images/003.jpg)

</div>
</div>

---

# Debilidades GANs

<div class="content center">

![width:700](assets/images/005.png)

Dificultad en aprender grandes distribuciones.
</div>


---
# ¿Por qué no basta con sólo GANs?

---
# ¿Por qué no basta con sólo GANs?


<div class="content">

![height:500](assets/images/004.png)

</div>

---

# Overview

<div class="content">

1. Introduction to Diffusion Models
2. Mathematical Foundation
3. Implementation Details
4. Results and Applications

</div>

---

# ¿Qué son los Modelos de Difusión?

<div class="content center">

_"Crear ruido desde los datos es fácil;
crear datos desde ruido es modelamiento generativo."_

Song et.al. 2020

</div>

---

# ¿Qué son los Modelos de Difusión?

<div class="content">

<div class="image-container">

![width:100%px](assets/images/001.png)

<span class="citation">Denoising Diffusion Probabilistic Models [DDPM], 2020</span>

</div>
</div>

---

# Denoising Diffusion Probabilistic Models (DDPMs)

<div class="content">

<div class="image-container">

DDPMs generan imágenes de forma progresiva a partir de ruido
![width:100%px](assets/images/007.PNG)

<span class="citation">Denoising Diffusion Probabilistic Models [DDPM], 2020</span>

</div>
</div>



---

# Denoising Diffusion Probabilistic Models (DDPMs)

<div class="content">

Para lograr que un modelo aprenda a generar imágenes a partir de ruido, necesitamos pares de entrenamiento en la forma de (ruido, imágenes).

Llamaremos al proceso que crea estos pares de entrenamiento **Forward Process**, encargado de convertir imágenes reales, en imágenes progresivamente más ruidosas.

![width:100%px](assets/images/006.PNG)

</div>

---

# Denoising Diffusion Probabilistic Models (DDPMs)

<div class="content">

Nuestro modelo generativo aprendererá a revertir este proceso (**Reverse Diffusion Process**). Es decir, progresivamente revertir el ruido de una imagen.

![width:100%px](assets/images/007.PNG)

Una vez entrenado correctamente, el modelo contará con la capacidad recibir una imagen de ruido, y convertirla en una "real". En otras palabras, ¡generará imágenes desde puro ruido!

</div>

---

# Denoising Diffusion Probabilistic Models (DDPMs)

<div class="content">

De manera más formal, los DDPMs iteran sobre $t$ pasos desde $0,1,...,T$

![width:100%px](assets/images/001.png)

- $x_0$ es la imagen original
- $q(x_t|x_{t-1})$ es el forward process
- $p_{\theta}(x_{t-1}|x_t)$ es el reverse diffusion process (aprendido por nuestro modelo con parámetros $\theta$)

</div>

---

# Forward Process

<div class="content">
<div class="center">

![width:900px](assets/images/forward_process.png)

</div>
<div class="theorem">

El proceso de difusión puede ser descrito como:

$$q(x_t|x_{t-1}) = \mathcal{N}(x_t; \sqrt{1-\beta_t}x_{t-1}, \beta_t\mathbf{I})$$

donde:
- $x_t$ es la imagen con ruido en el paso $t$
- $\beta_t$ es el planificador de ruido (_noise scheduler_)
- $\mathcal{N}$ denota una distribución normal

</div>
</div>

---

# Forward Process

<div class="content">

El proceso directo completo puede escribirse como:

$$q(x_{1:T}|x_0) = \prod_{t=1}^T q(x_t|x_{t-1})$$

y con el truco de reparametrización:

$$x_t = \sqrt{\alpha_t}x_0 + \sqrt{1-\alpha_t}\epsilon$$

donde

  - $\epsilon \sim \mathcal{N}(0, \mathbf{I})$
  - $\alpha_t = 1 - \beta_t$
  - $\bar{\alpha}_t = \prod_{i=1}^t \alpha_i$

</div>

---

# Truco de reparametrización

<div class="content">

y con el truco de reparametrización:

$$x_t = \sqrt{\alpha_t}x_0 + \sqrt{1-\alpha_t}\epsilon$$

<div class="center">

![width:900px](assets/images/ddpm_paper_nice_property.png)

</div>
</div>

---

# Forward Process

<div class="center">

<video controls width="90%">
  <source src="assets/videos/DiffusionProcess.mp4" type="video/mp4">
</video>

</div>

---

# Reverse process

<div class="center">

![width:900px](assets/images/reverse_process.png)

</div>

El proceso inverso puede ser descrito como:
$$p_\theta(x_{t-1}|x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t,t), \Sigma_\theta(x_t,t))$$

donde:
- $x_t$ es la imagen con ruido en el paso $t$
- $\mu_\theta$ es la media de ruido predicha por la red neuronal 
- $\Sigma_\theta$ es la varianza de ruido predicha
- $\theta$ son los parámetros del modelo
---

# Training Process Visualization

<div class="center">
<!-- Replace with your training visualization -->
<!-- <video controls width="80%">
  <source src="./assets/training.mp4" type="video/mp4">
</video> -->
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
  <source src="assets/videos/DiffusionProcess.mp4" type="video/mp4">
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
# Gracias!

## Preguntas?

GitHub: ouhenio
Email: eugenio.herrera@cenia.cl

---

# Referencias
Esta presentación está **fuertemente** inspirada por:

- [El material de las clases de modelos de Difusión de IE University](https://github.com/julioasotodv/ie-c4-466671-diffusion-models)
- [Denoising Autoencoders | Deep Learning Animated](https://www.youtube.com/watch?v=0V96wE7lY4w)
- El siguiente video
