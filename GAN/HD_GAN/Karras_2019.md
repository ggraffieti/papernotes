[[project](https://research.nvidia.com/publication/2019-06_A-Style-Based-Generator)] [[paper](https://arxiv.org/abs/1812.04948)] [[code](https://github.com/NVlabs/stylegan)] [[video](https://www.youtube.com/watch?v=kSLJriaOumA&feature=youtu.be)]

Inserted:<br>
Last revision:

# A Style-Based Generator Architecture for Generative Adversarial Networks
**Tero Karras, Samuli Laine, Timo Ailan**
<br>
<br>
This paper introduces an alternative architecture for the generator network, borrowing concepts from style transfer literature, which enable intuitive, scale specific control of the synthesis process. Furthermore, a comprehensive study of latent space disentanglement is performed, in order to demonstrate the strength of the proposed model.<br>**Note**: the architecture of the generator heavily relies upon the progressive growing model summarized [here](Karras_2018.md). Nevertheless the original contributions of this paper are totally uncoupled with the progressive growing model, and they can be adopted in any generator architecture (:telescope: search for experiments with non-growing architectures).
- The generator takes as input a learned constant code, and adjusts the style of the image at each convolutional layer with the guidance of a latent code, which controls the strength of the image features at different scales.
- Only the latent code controls the image synthesis process, and it is inserted , with some transformations, after each convolutional layer. The latent code is not simply drawn from a given distribution, but is processed in order to be disentangled.

#### Architecture

- Given a latent code _z_ in some latent space &Zscr; a non-linear mapping network (f : &Zscr; &rarr; &Wscr; ) produces _w_ &in; &Wscr;
- _w_ is then specialized by a set of learned affine transformations to a style _y_ = (y<sub>s</sub>, y<sub>b</sub>), that controls the adaptive instance normalization (AdaIN) operations, performed after each convolutional layer on the synthesis network:
<p align="center">
  <img src="img/karras2019_eq1.png">
</p>
where:<br>x<sub>i</sub> = feature map<br>y<sub>&#95;,i</sub> = scalar component of style _y_ &rarr; _y_ is 2*k dimensional, k = number of feature maps on the layer.

- This is similar to style transfer, but the style vector in computed from a feature vector in the latent space, and not from an image.
- Stochastic noise is added to the generator, in order to generate random details in the results (as the position of hairs, pores or freckles). The noise is a single channel "image", and it is broadcast to all the feature maps using learned per-feature scaling factors.
<p align="center">
  <img src="img/karras2019_model.png">
</p>
A block = affine transforms<br>B blocks = learned per-channel scaling factor.


- The network does not benefit from the introduction of latent code as input. The style is solely controlled by AdaIN layers.
- The AdaIN layers perform normalization (each channel is normalized to zero mean and unit variance), and only then scales and biases based on style are performed. Each style controls only one convolution, before being overridden by the next AdaIN operator.
- Thus, the effect of a single AdaIN layer is localized in the network i.e. modifying a specific subset of the style can be expected to affect only certain aspects of the image. Moreover, since the architecture exploits progressive growing, AdaIN layers localized in the first convolutional layers are responsible for the global style of the image (e.g. pose, sex), while AdaIN layers located in the high resolution part of the model are responsible of finer style characteristics, such as hair or skin color.

#### Stochastic variation
- Many aspects of human portraits can be regarded as stochastic, such as the placement of hairs, pores, freckles...
- StyleGAN adds per-pixel noise after each convolution. The noise only affects stochastic aspects of the result, leaving the overral structure and composition invariant and intact.
- The effect of noise is localized in the network. This is hypothesized to be guided by the fact that the generator has some "pressure" to introduce new content, as soon as new information is provided to the network. Hence, the easiest way to create stochastic variation is to rely on the introduced noise.
<p align="center">
  <img src="img/karras2019_noise.png">
</p>
Effect of noise inputs at different layers of the generator. (a) Noise is applied to all layers. (b) No noise. (c) Noise in fine layers only (64<sup>2</sup> – 1024<sup>2</sup>). (d) Noise in coarse layers only (4<sup>2</sup> – 32<sup>2</sup>). It can be seen that the artificial omission of noise leads to featureless “painterly” look. Coarse noise causes large-scale curling of hair and appearance of larger background features, while the fine noise brings out the finer curls of hair, finer background detail, and skin pores.

#### Disentanglement
- A disentangled latent space is the preferred goal of every generative model. A disentangled space is composed of linear subspaces, each of which controls a factor of variation. However, the sampling probability of every combination of factors in &Zscr; needs to match the corresponding density in the training data.<br>E.g. if we have only two independent factors of variation: hair color and height, a 2D latent space should model hair color in one dimension, and height in the other. At the same time, the sampling probability in &Zscr; has to respect the training set distribution. If very few people in the dataset have brown hair and are very high, the latent space sampling should embed this property, assigning low probability to the latent space sub-region that identifies tall brown-haired people.
- 
