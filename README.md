# Implementation for Spatial Transformer Network for Caffe

This code implemented the hot-off-the-press paper [Spatial Transformer Network](http://arxiv.org/abs/1506.02025) in one of the most popular deep learning frameworks -- [Caffe](http://caffe.berkeleyvision.org/).

The code contains both implementation for Spatial Transformer Layer (named with `st_layer`) and a regularizer on the magnitude of spatial transformations (named with `st_loss_layer`). The latter one is useful for adjusting how aggressive would you like your spatial transformer layer to perform.

## Organization of the Code

* **src/caffe/layers:** contains the GPU implementation (`.cpp` and `.cu` file) for Spatial Transformer Layer. Notice that CPU version is not refined and it will be mush faster to use GPU version. Since I did not test on CPU version too much, it may not even correct.
* **src/caffe/proto:** contains my proto definition for Spatial Transformer Layer (Search for 'SpatialTransformerParameter').
* **src/caffe/test:** containts a test file for Spatial Transformer Layer.
* **include:** contains the `.hpp` file for the layer.
* **examples:** contains several sample setting files (`.prototxt` files) for its usages on MNIST digits and CUB bird datasets. They should be useful in understanding how to use my Spatial Transformer layer. Notice that they may not be trainable on your local machines because I have another customized layers added to my version of caffe.

## Usage of Layer

* My layer only support affine transformation with six parameters received from the above layer as follows.
	
        T = [	\theta_11 \theta_12 \theta_13;
				\theta_21 \theta_22 \theta_23
		]
	
* It is not necessary that the above layer generates exactly six parameters. If it generates two (e.g. `\theta\_13` and `\theta\_23`, this is the case for only allowing translation to happen), you can indicate other four parameters in parameters for this Spatial Transformer Layer as follows.
	
          layer {
            name: "st_1"
            type: "SpatialTransformer"
            bottom: "data"
            bottom: "st/theta_1"
            top: "inc1/data"
            st_param {
              to_compute_dU: false
              output_H: 224
              output_W: 224
              theta_1_1: 0.5
              theta_1_2: 0
              theta_2_1: 0
              theta_2_2: 0.5
            }
          }
	
* In the above example, `output\_H` and `output\_W` is for indicating the output resolution, which can be differed from the resolution of input images.
* It is usually the case that Spatial Transformer Layer will be applied directly on input images to the networks. If this is the case, there is no need to backpropogate the loss to image pixels. One can set `to_compute_dU: false` in order to disable this useless backpropogation. The default value is `True`.
* One may find it extremely useful to refer to caffe.proto to see my definition for layer parameters of Spatial Transformer Layer.

## Acknowledges

This is the job done while I had internship at Cornell in Graphics and Vision Group, under supervision of Prof. Kavita Bala. I would like to thank great help from Kavita and her Ph.D. students Balazs Kovacs, Kevin Matzen and Sean Bell. 

## Contact

Webpage: http://www.kaichun-mo.com
