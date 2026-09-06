# Introduction
Diffusion models are generative models that used primarily for image generation and computer vision tasks.

Diffusion-based neural networks are trained through deep learning to progressively “diffuse” samples with random noise, then reverse that diffusion process to generate high-quality images.

Diffusion models are most prominently associated with image generation and other image processing tasks such as:
inpainting and super-resolution, but their applications extend to other domains including audio generation, drug design and molecule generation. For simplicity, ==this article will focus on image generation.==

> Note: you might see alot of physics and math in this article like thermodynamics


# History
Diffusion models is a type of AI that generates images and be used in other visual tasks (computer vision, inpainting, etc..). 
Overtime time, there was two teams of researches worked independently on Diffusion model then later both teams reached the same base result in different times.
the story here isn't just filling words, it shows that at least there's two original approaches came from and knowing how did they combined together later 


## History of evoultion

**Path 1 — Score-based generative models (SGMs), 2019**  
Song and Ermon built a model that didn't try to learn the probability of data directly. Instead, it learned the _gradient_ of that probability (called the "score function") — think of it as learning which direction to nudge data to make it more realistic, rather than learning how likely the data is outright. This sidesteps a messy math requirement called "normalization" (making sure total probability adds up to 1).

**Path 2 — Denoising Diffusion Probabilistic Models (DDPMs), 2020**  
Ho et al. took Sohl-Dickstein's earlier 2015 diffusion idea (add noise, then learn to reverse it) and combined it with a different math toolkit called variational inference — a technique for turning hard probability problems into easier, solvable ones.

**Where they merged**  
Ho et al. proved something important: their DDPM training method was mathematically _equivalent_ to the score-matching method used in SGMs. Same destination, different roads. So starting around 2020-2021, researchers stopped treating these as two competing techniques and started blending them — DDPMs became the dominant framework, but they absorbed ideas from the score-based approach along the way.
# How it works? 

## Nutshell (Quick)

The model is handed a clean image, then noise is added to it gradually in small steps (10% add noise, 20%, 30%... up to 100% — pure noise). This is called the **"forward process"** — and it's not learned by the model at all, it's just a fixed recipe of adding noise step by step. We do this so we always know exactly how much noise we added at each step (this matters later).

Now here's the clever part: since we know exactly how much noise we added at each step (because we added it ourselves), we can hand the model a noisy image and ask "what noise do you think is in here?" Then we compare his guess to the real noise we know we added, and correct him if he's wrong. Repeating this many times is how the model _learns_ to predict noise. This is called the **"reverse process"** — the part where actual learning/training happens.

Once trained, the model can now do this:

1. Predict the noise in a noisy image
2. Subtract a little of that noise
3. Repeat until the image shows clean details like edges, colors, and sharp details

## In Details (deeper)

### Forward process

The purpose of the forward diffusion process is to transform clean data from the training dataset, such as an image or audio sample, into pure noise. The most common method entails iteratively injecting gaussian noise until the entire data distribution is gaussian (natural noise).

at each timestamp, a small amount of Gaussian noise is added to X, then image is re-scaled to maintain a constant image size despite the continual injection of random pixels.
In this formulation X<sub>1</sub> is the clean image point, then small amounts of noise added to X and become X<sub>2</sub> and so on for X<sub>n</sub> 
**Adding noise**
The noise added at each step isn't the same amount every time — it _increases gradually_ as you go from step 1 to step T. This isn't random or careless; it's carefully tuned.

**Why vary it?** There's a tradeoff:

- **Too much noise** helps the model learn rare/underrepresented cases (like unusual poses or rare object types) because it forces the model to see lots of examples in "empty" or sparse areas of the data. But too much noise also wrecks the original image too fast, hurting accuracy.
  
- **Too little noise** preserves the original image better, but the model performs badly on those rare/underrepresented cases since it never gets pushed to explore them.

**The fix:** instead of picking one noise level, use _many different noise levels_ across the steps — light noise early, heavy noise later. Best of both worlds.

### Reverse Diffusion Process
This is the part of the architecture where actual learning happens — everything in the forward process was just a fixed recipe with no training involved. Here, the model is handed a noisy image and has to learn to reverse the damage, step by step, until it reconstructs something clean.

**The core task**

In theory, reverse diffusion is simply the mirror image of the forward process — if forward diffusion answers "given a clean-ish image, what does it look like with a bit more noise," reverse diffusion should answer "given a noisy image, what did it look like with a bit less noise." In practice, calculating that exact reverse relationship directly is mathematically intractable (too complex to solve directly), so the model doesn't try to compute it exactly. Instead, it learns to _approximate_ it using a neural network.

> **Intractable:** a problem that technically has a mathematical answer, but is too complex to actually calculate directly with available methods.

**What the model actually predicts**

A common misconception is that the model predicts the clean image, or predicts the exact amount of noise to remove at that specific step. It does neither. Instead, at each step, the model predicts the _entire_ noise it thinks is present in the current noisy image, then only removes a small fraction of that predicted noise (based on the same variance schedule used to add noise in the forward process) to move one step closer to a clean image.

This detail matters because it explains something important: since the structure of the noise added during the forward process was originally derived from the structure of the real image, a model that gets good at predicting that noise is, indirectly, also learning the underlying structure of real images — not just "what noise looks like."

**How the model is corrected (loss function)**

The specific comparison used to correct the model's guesses is closely related to a training method used in another generative architecture, Variational Autoencoders (VAEs).

> **Variational Autoencoder (VAE):** a different generative model architecture that learns to compress data into a smaller representation and reconstruct it back, using a similar probability-based training objective.

The model is optimized by trying to maximize a quantity called the **Variational Lower Bound (VLB)**, also called the **Evidence Lower Bound (ELBO)**.

> **VLB / ELBO:** since the exact "correct" probability of an image is mathematically intractable, this is a workaround — instead of measuring exact correctness, the model tries to maximize the best _guaranteed minimum_ estimate of how correct its predictions are.

This objective is built from three combined loss terms, each measuring **KL divergence**.

> **KL divergence:** a way to measure how different two probability distributions are from each other — in this case, how different the model's denoising guess is from the actual noising step that was really performed.

The three terms:

- One term compares the fully noised end result of the forward process to the model's starting point in reverse — this term can generally be ignored since it involves no learnable parameters.
- One term compares the model's denoising prediction at each step against the real noising step that happened at that same point in the forward process — this is the main term doing the real work across all the intermediate steps.
- One term measures how accurately the model predicts the final, fully clean image at the very last step.

Despite this complex derivation, all of this mathematically simplifies down to something much simpler in practice: the **mean-squared error (MSE)** between the noise the model predicted and the actual noise that was really added in the forward process, at each step.

> **Mean-Squared Error (MSE):** the average of the squared differences between predicted values and actual values — a very common way to measure how wrong a prediction is.

This is exactly why the model's output at every step is a _prediction of noise_, not a prediction of the clean image directly.

### Image Generation (using the trained model)

Once training is complete, generating a brand-new image works like this:

1. Start from a completely random noisy image, sampled from pure Gaussian noise.
2. Ask the trained model to predict the noise present in it.
3. Subtract a portion of that predicted noise.
4. Repeat this for however many steps are chosen.

An important detail: the number of steps used during _generation_ doesn't have to match the number of steps used during _training_. This is only possible because the model was trained to predict the full noise at each step, not a specific fixed amount tied to one exact step count.

This creates a practical tradeoff:

- **Fewer steps** → faster generation, cheaper computation, but less fine detail.
- **More steps** → better accuracy and detail, but slower and more computationally expensive.

# Guided Diffusion Models

A plain diffusion model just produces random variations similar to its training data — there's no way to ask it for something specific. Guided diffusion models solve this by letting a user steer the output toward a specific category or description.

The most common form is **text-to-image diffusion**, where a diffusion model is paired with a language model that interprets a text prompt (e.g., "a giraffe wearing a top hat") and feeds that meaning into the image generation process.

There are two main approaches to achieve this guidance:

- **Classifier-guided diffusion** — pairs the diffusion model with a separate classifier model that has learned a representation for each category it should be able to produce. No extra training is needed for the diffusion model itself, but it can only guide toward categories the classifier already knows.
- **Classifier-free guidance** — doesn't need a separate classifier model, but requires the diffusion model itself to be specifically trained for conditional guidance in two stages: first, a separate embedding model (like CLIP) converts the text prompt into a numeric representation; second, the diffusion model uses that representation to steer its output. This costs more to train upfront, but allows the model to handle prompt categories it never explicitly saw during training.

## Latent Diffusion Models

Standard diffusion models, despite producing high-quality results, are slow and computationally expensive — because the entire noising/denoising process happens directly on full-resolution pixel data. Latent diffusion models (the basis of Stable Diffusion) fix this.

The idea borrows again from VAEs: instead of running the diffusion process on the full-size image directly, first compress the image into a smaller, lower-dimensional representation (called a **latent representation**), and run the entire diffusion process there instead.

> **Latent representation:** a compressed, smaller-sized version of the original data that still preserves its important structure, used to reduce computation.

The full pipeline looks like this:

1. An encoder network compresses the input image into a smaller latent representation.
2. The standard diffusion process (forward and reverse) is applied entirely within that smaller latent space.
3. Once the reverse process produces a clean latent representation, a decoder network upsamples it back into a full-sized final image.

Because the diffusion process is working on much smaller data throughout, this dramatically reduces the computational cost compared to running diffusion directly on full-resolution pixels.
