# Heuristics For Data Efficient Deep Learning: Max Loss Increase & Gradient Loss

This repository contains code for the paper [Heuristics For Data Efficient Deep Learning: Max Loss Increase & Gradient Loss].

## Citation
This code is built off of the paper An Empirical Study of Example Forgetting during Deep Neural Networks and the repo of Toneva et al. found at https://github.com/mtoneva/example_forgetting. We added the algorithms for computing Max Loss Increase (MLI) and data pruning based on MLI, along with added package compatability. 

If you build on the work in this paper, please cite the following: 

@article{DBLP:journals/corr/abs-1812-05159,
  author       = {Mariya Toneva and
                  Alessandro Sordoni and
                  Remi Tachet des Combes and
                  Adam Trischler and
                  Yoshua Bengio and
                  Geoffrey J. Gordon},
  title        = {An Empirical Study of Example Forgetting during Deep Neural Network
                  Learning},
  journal      = {CoRR},
  volume       = {abs/1812.05159},
  year         = {2018},
  url          = {http://arxiv.org/abs/1812.05159},
  eprinttype    = {arXiv},
  eprint       = {1812.05159},
  timestamp    = {Tue, 01 Jan 2019 15:01:25 +0100},
  biburl       = {https://dblp.org/rec/journals/corr/abs-1812-05159.bib},
  bibsource    = {dblp computer science bibliography, https://dblp.org}
}

##### MNIST and permuted MNIST:

To run baseline model (no removal): 
```
python run_mnist.py 
    --dataset [mnist/permuted_mnist]
    --no_dropout 
    --output_dir [mnist/permuted_mnist]_results
    --seed s
```
, where s ranges from to 1 to 5. The default setting was used for all other flags. Use options `--dataset mnist` and `--output_dir mnist_results` to run on MNIST, and options `--dataset permuted_mnist` and `--output_dir permuted_mnist_results` for permuted MNIST. Each training run with a different seed saves a file that contains the presentation statistics (loss, accuracy, misclassification margin) from that run in the specified `--output_dir`. The names of the saved files contain the arguments (and argument values) that were used to generate them.

To order the examples by both forgetting and Max Loss Increase, run the following 

```
python order_examples_by_forgetting.py 
    --output_dir [mnist/permuted_mnist]_results 
    --output_name [mnist/permuted_mnist]_sorted
    --output_dir_MLI [mnist/permuted_mnist]_sorted_MLI
    --input_dir [mnist/permuted_mnist]_results_MLI
    --input_fname_args 
            dataset [mnist/permuted_mnist] 
            no_dropout True 
            sorting_file none 
            remove_n 0 
            keep_lowest_n 0
```
This command calculates the forgetting score and the max loss increase for each example in the dataset, and sorts based off these scores respectively (ascending). It saves the data in .pkl files in the output_dir specified. 

##### CIFAR-10 and CIFAR-100:

Run baseline model. 

```
python run_cifar.py 
    --dataset [cifar10/cifar100] 
    --data_augmentation 
    --output_dir [cifar10/cifar100]_results
    --seed s
```
Sort by Forgetting Score and by Max Loss increase 

```
python order_examples_by_forgetting.py 
    --output_dir [cifar10/cifar100]_results 
    --output_name [cifar10/cifar100]_sorted
    --output_dir_MLI [ifar10/cifar100]_sorted_MLI
    --input_dir [ifar10/cifar100]_results_MLI
    --input_dir [cifar10/cifar100]_results 
    --input_fname_args 
            dataset [cifar10/cifar100]
            data_augmentation True 
            cutout False 
            sorting_file none 
            remove_n 0 
            keep_lowest_n 0 
            remove_subsample 0 
            noise_percent_labels 0 
            noise_percent_pixels 0 
            noise_std_pixels 0
```


## Experiments

#### Removing examples from training set

The removal experiments specify a number of examples to be completely removed from the sorted training set. MLI and Forgetting score have different sorting files to be used, depending on by which heuristic you are sorting. This is specified through the `--sorting_file` argument. Select `keep_lowert_n = -1` for random removal. 

To sort by Max Loss Increase
```
python run_cifar.py 
    --dataset cifar10 
    --data_augmentation 
    --cutout 
    --sorting_file cifar10_sorted_MLI
    --input_dir cifar10_results_MLI
    --output_dir cifar10_results_MLI
    --seed s 
    --remove_n r 
    --keep_lowest_n k
```
, where s is in `range(1,6)`, r is in `range(0,50000,1000)`, and k is 0 (for selected) and -1 (for random).


ects forgetting. We introduce label noise by assigning random labels to a specified percentage of the training set.

For Figure 3 results:
```
python run_cifar.py 
    --dataset cifar10 
    --data_augmentation 
    --output_dir cifar10_results 
    --noise_percent_labels 20
```
