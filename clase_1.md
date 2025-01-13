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

# Modelos de Difusión I
## Diplomado Universidad de Tarapacá

Eugenio Herrera-Berg
Centro Nacional de Inteligencia Artificial

</div>

---

# Un poco sobre mí
<div class="content">

<div class="columns">

<div class="content">

Alguno de mis proyectos Cenia:
- Latam-GPT: GPT 100% Latinoamericano
- Rial-AI: startup de Virtual Try-On
- Araucaria 🖌️

En una vida pasada:
- Trabajé en NotCo (2019-2020)
- Hice que la StyleGAN3 fuese guiada por texto (2020) 

</div>
<div class="center">

<video controls autoplay loop muted width="90%">
  <source src="assets/videos/nicanor.mp4" type="video/mp4">
</video>

<span class="citation">"A painting of Nicanor Parra", 2020</span>

</div>

---

# Overview

<div class="content">

1. Repaso modelos generativos
2. Modelos de difusión
  2.1 Denoising Diffusion Probabilistic Models
  2.2 Forward y Backward Process
  2.3 Loss
  2.4 Unet
3. Avances Formulación

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


<div class="content">

![height:500](assets/images/004.png)

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

<div class="center">

<video controls width="90%">
  <source src="assets/videos/DiffusionProcess.mp4" type="video/mp4">
</video>

</div>

---

# Forward Process

<div class="content">
<div class="theorem">

El proceso directo completo puede escribirse como:

$$q(x_{1:T}|x_0) = \prod_{t=1}^T q(x_t|x_{t-1})$$

y con el truco de reparametrización:

$$x_t = \sqrt{\alpha_t}x_0 + \sqrt{1-\alpha_t}\epsilon$$

donde

  - $\epsilon \sim \mathcal{N}(0, \mathbf{I})$
  - $\alpha_t = 1 - \beta_t$
  - $\bar{\alpha}_t = \prod_{i=1}^t \alpha_i$

</div>
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

# Reverse process

<div class="center">

![width:900px](assets/images/reverse_process.png)

</div>

El proceso inverso puede ser descrito como:
$$p_\theta(x_{t-1}|x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t,t), \Sigma_\theta(x_t,t))$$

donde:
- $x_t$ es la imagen con ruido en el paso $t$
- $\mu_\theta$ es la media de ruido predicha por la red neuronal 
- $\Sigma_\theta$ corresponde a la varianza de $\beta_t$ (no se aprende)
- $\theta$ son los parámetros del modelo

---

# Reverse process

<div class="center">

![width:900px](assets/images/reverse_process.png)

</div>

Entrenaremos un modelo $p_\theta$ para que aprenda a revertir el proceso. 

Comenzando por $p(x_T) = \mathcal{N}(0, I)$, recreará la imagen siguiendo:

$$p_\theta(x_{0:T}) := p(x_T) \prod_{t=1}^T p_\theta(x_{t-1} | x_t)$$

---

# Resumen

<div class="content center">

![](assets/images/resume.PNG)

</div>

---

# Loss

<div class="content">

Originalmente, la pérdida en DDPMs corresponde al límite inferior variacional entre

$$L_{VLB} = \mathbb{E}_{q(x_{0:T})} \left[ -\log p(x_T) + \sum_{t=1}^T \left\| \frac{q(x_{t-1}|x_t,x_0)}{p_\theta(x_{t-1}|x_t)} \right\| \right]$$

En la práctica, esta se simplifica al Error Mínimo Cuadrado entre

$$L_{simple} = \mathbb{E}_{t,x_0,\epsilon} \left[ \|\epsilon - \epsilon_\theta(x_t,t)\|^2 \right]$$

Donde

- $\epsilon$ es el ruido agregado en el forward pass
- $\epsilon_\theta(x_t,t)$ es el ruido predecido por el modelo en el paso $t$

</div>

---

# U-Net y DDPMs

<div class="content">
<div class="columns">

<div>
La U-Net es una CNN que:

- Procesa imágenes a múltiples escalas

- Procesa paralelamente detalles locales y globales

- Utiliza conexiones residuales (*skip connections*)

- Recibe como input:

  - Imagen ruidosa $x_t$
  - Paso de tiempo $t$ (embedding)

</div>

<div class="content center">

![width:600px](assets/images/unet.png)

</div>

</div>
</div>

---

# U-Net y DDPMs

<div class="content">

La U-Net actúa como el modelo $\epsilon_\theta$ que predice el ruido en cada paso:

$$L_{simple} = \mathbb{E}_{t,x_0,\epsilon} \left[ \|\epsilon - \epsilon_\theta(x_t,t)\|^2 \right]$$

donde para $\epsilon_\theta(x_t,t)$ la U-Net:
- Recibe la imagen ruidosa $x_t$ y el paso $t$
- Predice el ruido $\epsilon$ que se agregó
- Permite calcular la media $\mu_\theta$ para el proceso inverso

</div>

---

# Avances Teóricos en Modelos de Difusión

<div class="content">

Los modelos de difusión han evolucionado significativamente desde DDPMs:

- **DDIM** (2020): Muestreo determinístico y acelerado
- **Score-based Models** (2020): Gradientes de score matching
- **Probability Flow ODE** (2021): Formulación continua
- **SDE** (2021): Marco teórico unificado
- **EDM** (2023): Mejoras prácticas y eficiencia

</div>

---
# Denoising Diffusion Implicit Models (DDIM) [2020]

<div class="content">

DDIM propone un proceso de muestreo determinístico que:
- **Reduce el número de pasos necesarios (de 1000 a ~50)**
- Mantiene la calidad de las imágenes
- Permite interpolación significativa en el espacio latente
- No requiere reentrenamiento del modelo

$$\text{DDIM: } \mathbb{E}[x_{t-1}|x_t,x_0] = \sqrt{\alpha_{t-1}}\hat{x}_0 + \sqrt{1-\alpha_{t-1}}\bar{\epsilon}_\theta(x_t,t)$$

</div>

---
# Score-based Generative Modeling [2020]

<div class="content">

Reformula la generación como un problema de estimación de gradientes:
- **Entrena el modelo para estimar $\nabla_x \log p(x)$**
- Unifica diferentes tipos de ruido
- Permite muestreo continuo
- **Base teórica para posteriores avances (SDE, Flow)**

$$\text{Score function: } s_\theta(x,t) = \nabla_x \log p_t(x)$$

</div>

---
# Probability Flow ODE [2021]

<div class="content">

Reformulación del **proceso de difusión como una ODE**:
- Permite genación determinística
- Tiempo continuo vs discreto
- Base matemática más sólida
- Conexión con normalizing flows

$$\frac{dx}{dt} = f(x,t) + g(t)^2 \nabla_x \log p_t(x)$$

</div>

---
# SDE Formulation [2021]

<div class="content">

Marco teórico unificado que:
- **Generaliza modelos previos (Score, Flow, DDPM)**
- Permite diferentes tipos de ruido
- Provee nuevos métodos de muestreo
- Base matemática rigurosa

$$dx = f(x,t)dt + g(t)dw$$

donde $f(x,t)$ es el _drift_ y $g(t)$ es el _coeficiente de difusión_

</div>

---
# Elucidating Diffusion Models (EDM) [2023]

<div class="content">

Mejoras prácticas para entrenamiento y sampling:
- Nueva parametrización del ruido
- Mejor planificador de ruido (_noise scheduler_)
- Muestreo más eficiente
- **Estado del arte en calidad/velocidad**
- **Permite modelar todos los trabajos anteriores bajo el mismo framework**

$$L_{EDM} = \mathbb{E}_{t,x_0,\epsilon} \left[ w(t)\|\epsilon - D_\theta(x_t,t)\|^2 \right]$$

donde $w(t)$ es una función de peso optimizada
</div>

---

# Gracias!

<div class="content">

## ¿Preguntas?

GitHub: ouhenio
Email: eugenio.herrera@cenia.cl

</div>

---

# Referencias

<div class="content">

Esta presentación está **fuertemente** inspirada por:

- [El material de las clases de modelos de Difusión de IE University](https://github.com/julioasotodv/ie-c4-466671-diffusion-models)
- [Denoising Autoencoders | Deep Learning Animated](https://www.youtube.com/watch?v=0V96wE7lY4w)

</div>
