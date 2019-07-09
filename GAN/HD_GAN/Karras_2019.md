[[project](https://research.nvidia.com/publication/2019-06_A-Style-Based-Generator)] [[paper](https://arxiv.org/abs/1812.04948)] [[code](https://github.com/NVlabs/stylegan)] [[video](https://www.youtube.com/watch?v=kSLJriaOumA&feature=youtu.be)]

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
