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
    padding: 0.2em;
    margin: .7em 0;
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

# Modelos de Difusión II
## Diplomado Universidad de Tarapacá

Eugenio Herrera-Berg
Centro Nacional de Inteligencia Artificial

</div>

---

# Overview

<div class="content">


1. Comparación GANs vs VAEs vs Difusión
2. Generación Condicional
3. Precursores text-to-img
4. Large Difusion Models
5. Avances en Manipulación

</div>

---

# Comparando modelos generativos en CV

<div class="content">

| Métrica | GANs | VAEs | Modelos de Difusión |
|---------|------|------|---------------------|
| **Calidad de Muestras** | Alta fidelidad | Borrosas, pérdida de detalles | Alta calidad |
| **Velocidad de Sampling** | Muy rápido (1 forward pass) | Muy rápido (1 forward pass) | Lento (50-1000 pasos) |
| **Cobertura de Modos** | Pobre (mode collapse) | Excelente (regularización KL) | Excelente |

</div>

---

# Generative learning trilemma

<div class="center content">

![width:500px](assets/images/trilemma.png)

</div>

---

# Generación Condicional

<div class="content">

Hasta el momento hemos visto modelos de difusión capaces de generar imágenes, pero sin un control asociado a esta generación.

Los modelos de difusión pueden ser condicionados para generar imágenes específicas basadas en:

- Texto
- Imágenes
- Señal cualquiera

Esto se logra a través de diferentes técnicas de **_guidance_**.

</div>

---

# Classifier Guidance

<div class="content">

<div class="theorem">

La guidance por clasificador utiliza un clasificador pre-entrenado para guiar el proceso de difusión:

$$p_\gamma(x|y) \propto p(x) \cdot p(y|x)^\gamma$$

donde:
- $p(y|x)$ es un clasificador entrenado con pares de imágenes ruidosas
- $y$ es la condición (clase)
- $\gamma$ determina la influencia del clasificador en la generación de la imagen

</div>

El gradiente del clasificador se usa para "empujar" la generación hacia la condición deseada.

</div>

---

# Classifier-free Guidance

<div class="content">

Propuesta por Ho & Salimans (2021), elimina la necesidad de un clasificador separado:

<div class="theorem">

El gradiente del score puede expresarse como una interpolación entre el score no condicionado y el condicionado:

$$\nabla_x \log p_\gamma(x|y) = (1-\gamma)\nabla_x \log p(x) + \gamma\nabla_x \log p(x|y)$$

donde:
- $\gamma$ es el peso de guidance
- $p(x)$ es la distribución no condicionada
- $p(x|y)$ es la distribución condicionada

<!-- Este enfoque:
- Es más eficiente computacionalmente
- No requiere entrenar un clasificador adicional
- Permite control más directo sobre la fuerza del conditioning -->

</div>

</div>

---

# Classifier-free Guidance

<div class="center content">

![width:1000px](assets/images/classifier_free_guidance.png)

</div>

---

# DALL-E (2021)


<div class="content center">

![width:1000px](assets/images/dalle1.png)

</div>

---

# DALL-E (2021)

<div class="content">

## Arquitectura Compuesta

<div class="columns">

<div>

**Stage 1: dVAE (discrete VAE)**
   - Comprime imágenes a tokens discretos
   - Reduce 256x256x3 a grid de 32x32 tokens (de 8192 valores posibles)
   - Permite representar imágenes como secuencias de tokens

</div>
<div>

**Stage 2: Transformer Autoregresivo**
   - 12B parámetros
   - Arquitectura GPT-3 modificada
   - Entrada: [tokens_texto, tokens_imagen]
   - Entrenado para predecir tokens de imagen dado el texto
   - Genera tokens de imagen uno a uno

</div>
</div>

---

# CLIP (2021)

<div class="content">

CLIP (Contrastive Language-Image Pre-training) es un modelo que:

- Aprende representaciones conjuntas de texto e imagen
- Es entrenado con 400M de pares imagen-texto de internet

OpenAI liberó sus pesos al publicar el paper, demostrando sus fuertes capacidades de clasificación.

</div>

---

# CLIP (2021)

<div class="content">

<div class="center">
<div class="image-container">

![width:600px](assets/images/clip.svg)

<span class="citation">Contrastive Language-Image Pre-training [OpenAI], 2021</span>

</div>
</div>

</div>

--- 

# [inserte modelo]+CLIP (2021-2022)

<div class="content">

A los pocos días de ser liberado CLIP, ocurre un fenómeno particular en la (entonces pequeña) comunidad online de los aficionados por los modelos generativos en CV.

Un puñado de investigadores y desarolladores independientes comienzan a utilizar CLIP para guiar la generación de imágenes.

Todo esto ocurre en twitter y discord, donde comparten tips, y finalmente logran resultados equiparables con Dall-e.

</div>

---

# VQGAN+CLIP (2021-2022)

<div class="content">

<div class="center">

![height:500px](assets/images/vqgan.png)

<span class="citation">VQGAN+CLIP, Katherine Crowson (2021)</span>

</div>

</div>

---

# Large Diffusion Models

<div class="content">

- Stable Diffusion (Latent Diffusion)
- DALL-E 2
- Imagen
- Flux

</div>

---

# (Stable) Latent Diffusion (2021)

<div class="content">

Durante esta fiebre de experimentación open source, un departamento de la Universidad de Heidelberg libera el paper Latent Diffusion, una arquitectura compuesta:

<div class="center">

![width:700px](assets/images/latent_diffusion.PNG)

</div>

</div>

---

# (Stable) Latent Diffusion (2021)


<div class="content">

En resumen:

1. Reduce dimensionalidad con encoder
2. Aplica difusión en espacio latente comprimido
3. Decoder recupera imagen final

Ventajas:
- Menor consumo de memoria
- Generación más rápida
- Mantiene calidad visual


</div>

---


# DALL-E 2 (2022)

<div class="content">

<div class="center">
<div class="image-container">

![width:700px](assets/images/dalle2.PNG)

<span class="citation">Hierarchical Text-Conditional Image Generation [OpenAI], 2022</span>

</div>
</div>

</div>

---

# Imagen (2022)

<div class="content columns">

  <div class="content">

  Imagen de Google introduce:

  - Arquitectura U-Net en cascada
  - Múltiples niveles de resolución
  - Transformer para conditioning
  - T5 para embeddings de texto

  </div>

<div class="center">

![height:400px](assets/images/imagen.PNG)

<span class="citation">Photorealistic Text-to-Image Diffusion Models [Google], 2022</span>

</div>

</div>

---

# Flux (2024)

<div class="center content">

![height:500px](assets/images/flux_2.png)

<span class="citation">Flux.1 [Black Forest Labs], 2022</span>

</div>

---

# Flux (2024)

<div class="content columns">

  <div class="content">

  Modelo abierto* de los creadores de latent diffusion:

  - Reemplazan U-Net por Diffusion Transformer
  - Usan Rectified-Flow para entrenar el modelo
  - Estado del arte en generación de imágenes

  </div>

<div class="center">

![height:450px](assets/images/flux.png)

</div>
</div>


---

# Avances en Manipulación

<div class="content">

Técnicas recientes permiten:

- Personalización de modelos
- Control fino de la generación
- Edición de imágenes existentes
- Preservación de identidad

</div>

---

# Textual Inversion (2022)

<div class="center content">

![height:400px](assets/images/textual_inversion.png)
<span class="citation">An Image is Worth One Word: Personalizing Text-to-Image Generation using Textual Inversion [Tel-Aviv University], 2022</span>

</div>

---

# Textual Inversion (2022)

<div class="content">

Permite "enseñar" nuevos conceptos al modelo:

<div class="theorem">

1. Se optimiza un embedding de texto específico
2. Se mantienen los pesos del modelo base
3. Se aprende a representar un concepto nuevo

$$L = \mathbb{E}_{x,t,\epsilon}[\|\epsilon - \epsilon_\theta(x_t,t,S^*)\|^2]$$

donde $S^*$ es el nuevo token aprendido

</div>

</div>

---

# Null-text Inversion (2022)

<div class="center content">

![height:500px](assets/images/null_text_inv_2.png)
<span class="citation">Null-text Inversion for Editing Real Images using Guided Diffusion Models [Google], 2022</span>

</div>

---

# Null-text Inversion (2022)

<div class="content">

Permite edición de imágenes a partir de texto:


1. Encuentra el prompt nulo óptimo para reconstruir una imagen
2. Usa este prompt como base para ediciones
3. Preserva mejor las características originales

$$L_{null} = \|\epsilon - \epsilon_\theta(x_t,t,\emptyset)\|^2 + \lambda\|x_0 - \hat{x}_0\|^2$$

<div class="center">

![width:600px](assets/images/null_text_inv.png)

</div>

</div>

---

# DreamBooth

<div class="content">

<div class="center">
<div class="image-container">

![width:1000px](assets/images/dreambooth.png)

<span class="citation">DreamBooth: Fine Tuning Text-to-Image Diffusion Models [Google], 2022</span>

</div>
</div>

</div>

---

# DreamBooth

<div class="content columns">

<div>

Permite personalización con pocas imágenes:

- Fine-tuning específico por sujeto (del modelo completo)
- Usa token único identificador (flexible)
- Preserva capacidades generales
- Requiere 3-5 imágenes de referencia

</div>
<div>

![width:1000px](assets/images/dreambooth_2.png)

</div>
</div>

</div>

---

# ControlNet (2023)

<div class="content center">

![width:1000px](assets/images/controlnet.png)

<span class="citation">Adding Conditional Control to Text-to-Image Diffusion Models [Stanford], 2023</span>

</div>

---

# ControlNet (2023)

<div class="content columns">

<div class="content">
Permite control preciso de la generación:


- Agrega redes de control a U-Net
- Mantiene pesos originales
- Soporta múltiples condiciones:
  - Poses
  - Profundidad
  - Segmentación
  - Canny edges

</div>

<div class="content center">

![width:400px](assets/images/controlnet_2.png)

</div>
</div>

</div>

---

# IP-Adapter (2023)

<div class="content center">

![width:1000px](assets/images/ip-adapter.png)

<span class="citation">IP-Adapter: Text Compatible Image Prompt Adapter for
Text-to-Image Diffusion Models [Tencent], 2023</span>

</div>

---

# IP-Adapter (2023)

<div class="content columns">

<div class="content">
Permite control preciso de la generación:


- Agrega adaptadores livianos y paralelos al de texto cross-attention
- No requiere fine-tuning del modelo base
- Soporta múltiples condiciones basadas en imágenes

</div>

<div class="content center">

![width:600px](assets/images/ip-adapter_2.png)

</div>
</div>

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

- DALL-E 2: Ramesh et al. (2022)
- Stable Diffusion: Rombach et al. (2022)
- Imagen: Saharia et al. (2022)
- DreamBooth: Ruiz et al. (2022)
- ControlNet: Zhang et al. (2023)

</div>