## Towards Detecting, Augmenting, and Sampling, Efficient Datapoints for Robust Out-of-Distribution Generalization

Script with the steps to run can be found in `run_details.pdf` in this reporsitory.

Paper Reference: 

- Arxiv: https://arxiv.org/abs/2109.06827
- Official: https://aclanthology.org/2021.emnlp-main.835/

## Environment
Please use the .yml file to set up the new environment before running the experiments. We used python version 3.10.

## Files
- `roberta_fine_tune.py` is used to finetune the Roberta models.
- `msp_eval.py` are used to find the MSPs of a dataset pair's examples using the finetuned model.

## How to run
These steps show how to train calibration models on the SST2 dataset, and evaluated against IMDB.

A differet dataset pair can be used by updating the approriate `dataset_name` or `id_data`/`ood_data` values as shown below:


### Training the Calibration Model (RoBERTa)
1. Using HF Datasets -
   ```
   id_data="sst2"
   nohup python -u roberta_fine_tune.py  --batch_size 16 --fname roberta_sst2 --output_dir roberta_ckpts_sst2/ --task_name sst2 > roberta_sst2
   ```


### Finding Maximum Softmax Probability (MSP)
1. Using HF Datasets -
   ```
   id_data="sst2"
   ood_data="imdb"
   python msp_eval.py --model_path roberta_ckpts/roberta-$id_data --dataset_name $ood_data --fname ${id_data}_$ood_data
   ```

### Evaluating AUROC
1. Compute AUROC of MSP -
    ```
   import utils
   id_data = 'sst2'
   ood_data = 'imdb'
   id_msp = utils.read_model_out(f'output/roberta/roberta_{id_data}_msp.npy')
   ood_msp = utils.read_model_out(f'output/msp/{id_data}_{ood_data}_msp.npy')
   score = utils.compute_auroc(-id_msp, -ood_msp)
   
    ```


Abstract = "As most machine learning models are trained and evaluated on datasets that are independent and identically distributed (i.i.d), their perfor-
mance falters when faced with samples that are adversarial or statistically out-of-distributed (OOD). Thus, in this project, we work towards robust out-of-distribution generalization without having access to OOD samples. Specifically, we propose a threefold approach for training deep neural networks. To begin with, we identify samples that are easy to learn, mislabeled, and hard to learn using the model’s training dynamics. Then we proffer a mixup based data augmentation technique that generates effective synthetic samples intelligently combining easy and difficult samples that contribute towards improving model robustness and generalization capabilities. Lastly, we plan on adopting a non-trivial sampling scheme that makes the learning procedure more efficient. In this report, we begin by experimenting with detecting and calibrating large language models for out-of-distribution detection by reproducing experiments of Arora et al. (2021).",


