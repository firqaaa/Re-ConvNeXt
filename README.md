# ConvNext
<p style="font-size: 16px">ConvNeXt Re-implementation in PyTorch</p>
<p style="font-size: 16px">Hierarchical Transformers (e.g., Swin Transformers[1]) has made Transformers highly competitive as a generic vision backbone and in a wide variety of vision tasks. A new paper from Facebook AI Research — “A ConvNet for the 2020s”[2] — gradually and systematically “modernizes” a standard ResNet[3] toward the design of a vision Transformer. The result is a family of pure ConvNet models dubbed ConvNeXt that compete favorably with Transformers in terms of accuracy and scalability.</p>

## The “Modernization” Process

<!-- <img src="/home/firqaaa/Notebook/Python/Re-ConvNeXt/assets/fig-2.png"><br> -->

![image info](./assets/fig-2.png)

<p style="font-size: 16px">(All models are trained and evaluated on ImageNet-1K. But for this case, i use CIFAR-10)</p>

<p style="font-size: 16px">There's two important things in building a ConvNeXt. A <b>Macro</b> and <b>Micro</b> design.</p>

## Macro Design
![image info](./assets/table-9.png)

* <p style="font-size: 16px">Stage compute ratio: the number of blocks in each stage goes from (3, 4, 6, 3) to (3, 3, 9, 3) (see Table 9) to make the compute ratio 1:1:3:1.</p>
* <p style="font-size: 16px">Patchifying stem: a 7x7 convolution with stride 2 followed by a 3x3 max pooling with stride 2 -> a 4x4 convolution with stride 4 (from overlapping to non-overlapping).</p>
* <p style="font-size: 16px">ResNeXt-ify: use depthwise convolution and increase the network width.</p>
* <p style="font-size: 16px">Inverted Bottleneck: instead of 384 -> 96 -> 384, the network widths (number of channels) becomes 96 -> 384 -> 96 ((a) to (b) in Fig 3). (Note that the number of channels in the last block of (b) is wrong. It should be 384 -> 96.)</p>
* <p style="font-size: 16px">Larger kernel size: use a 7x7 convolution instead 3x3 and move the convolution layer up ((b) to (c) in Fig 3).</p>

![image info](./assets/fig-3.png)

## Micro Design
![image info](./assets/197199637-9cd8b61b-632a-4cad-9361-b2db0af8c574.png)

* <p style="font-size: 16px">Replacing ReLU with GELU</p>
* <p style="font-size: 16px">Fewer activation function: from three to only one between the two 1x1 layers. This has been proven to be very effective (a 0.7% boost).</p>
* <p style="font-size: 16px">Fewer normalization layers: from three to only one between the 7x7 and the 1x1 layers.</p>
* <p style="font-size: 16px">Substituting BN with LN: the Layer Normalization version perform slightly better.</p>
* <p style="font-size: 16px">Separate downsampling layers: the downsampling layer consists of a Layer Normalization and a 2x2 convolution with stride 2. They are added between stages.* </p>