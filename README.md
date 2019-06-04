# gqnrs

DeepMind's Generative Query Network (GQN) implementation in Rust. It relies on [tch-rs](https://github.com/LaurentMazare/tch-rs), the libtorch binding in Rust, and [tfrecord-rs](https://github.com/jerry73204/tfrecord-rs/tree/master) for data serving. The repo is mainly done by jerry73204 for [NTU NEWSLAB](https://newslabcpsgroupwebsite.wordpress.com/)'s research project.

## Build

### Prerequisites

The program requires CUDA 9.0 and libtorch to work.

- Rust 2018 edition

  Install Rust package on your OS/distribution through package manager or something else. I would recommend [rustup](https://rustup.rs/).

- libtorch

  Visit [PyTorch site](https://pytorch.org/) or click this [direct link](https://download.pytorch.org/libtorch/cu90/libtorch-shared-with-deps-latest.zip) to download libtorch binary pacakge.

- CUDA 9.0

  Visit NVIDIA CUDA Toolkit 9.0 [download page](https://developer.nvidia.com/cuda-90-download-archive) and install CUDA.



### Compile

This is example invocation to build gqnrs executable. We assume unzipped libtorch at `/path/to/libtorch`, and CUDA 9.0 library path `/usr/local/cuda-9.0/lib64`.


```sh
env LIBTORCH=/path/to/libtorch \
    LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:/path/to/libtorch/lib \
    cargo build --release
```

## Dataset

Obtain [gqn-dataset](https://github.com/deepmind/gqn-datasets). The overall dataset has about 2TB size. Make sure you have enough storage.

## Usage

### Example Usage

Suppose we want to train on `rooms_free_camera_with_object_rotations` dataset, located at `/path/to/gqn-dataset/rooms_free_camera_with_object_rotations`. We save the model file `model.zip` during training.

```sh
env LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:/path/to/libtorch/lib \
    RUST_LOG=info \
    cargo run --release -- \
                        -n rooms_free_camera_with_object_rotations \
                        -i /path/to/gqn-dataset/rooms_free_camera_with_object_rotations \
                        --model-file model.zip
```

### Advanced Usage

The program provides several advanced features, including

- **Logging**: Save the losses and model outputs in `logs` directory with `--log-dir logs`.
- **Multi-GPU training**: Specify allocated devices by `--devices 'cuda(0),cuda(1)'`, and set the batch size per device by `--batch-size 4`.
- **Tweak logging**: Save model every 1000 steps by `--save-steps 1000`, and write logs every 100 steps by `--log-steps 100`. You can save output images by `--save-image`.


```sh
env LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:/path/to/libtorch/lib \
    RUST_LOG=info \
    cargo run --release -- \
                        -n rooms_free_camera_with_object_rotations \
                        -i /path/to/gqn-dataset/rooms_free_camera_with_object_rotations \
                        --model-file model.zip \
                        --log-dir logs \
                        --batch-size 2 \
                        --devices 'cuda(0),cuda(1)' \
                        --save-steps 1000 \
                        --log-steps 100 \
                        --save-image
```


## Visualization

You can plot the loss curve using Facebooks' [Visdom](https://github.com/facebookresearch/visdom). The helper script `plot.py` visualizes the learning progress.

Check the instructions on official site to install Visdom, and start the Visdom server.

```sh
visdom
```

Let gqnrs saves the logs by `--log-dir logs` option. Run `./plot.py` to push to logs to visdom server.

```sh
./plot.py logs
```
