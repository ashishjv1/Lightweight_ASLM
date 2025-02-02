# Pedestrian Attribute Recognition Using Lightweight Attribute-Specific Localization Model
Compression of  Convolutional Layers in Attribute-Specific Localization Model using CPD-EPC, SVD Decompositions

## **Required Environments**
* Python 3.7 +
* Pytorch 1.11
* Flopco-pytorch 
* Numpy
* Tensorly 0.4.4

## **Datasets**

* PETA: http://mmlab.ie.cuhk.edu.hk/projects/PETA.html
* PA-100K: https://github.com/xh-liu/HydraPlus-Net

## Labels 
* data_labels/peta
* data_labels/pa-100k

### **Steps to Reproduce**
1. Use `"run_main.py"` to save best checkpoint for the dataset. <br />
   ```
    python3 run_main.py --model=FULL --attr_num=26 --experiment=PA-100K --epoch=15 --checkpoint_save=path_to_save_checkpoints
   ```

2. Use `"make_decompositios.py"` for full model decomposition using SVD or CPD-EPC. <br />
   ```
   python3 make_decompositios.py --dpath=path_to_dataset --mpath=path_to_saved_checkpoint --tlabels=path_to_train_labels --vlabels=path_to_val_labels --factors=both --attr_num=26 --experiment=PA-100K --device=cpu
   ```
   To Submit Slurm jobs for full model decompositions:
   ```
   python3 submit_decompositios_slurm.py --dpath=path_to_dataset --mpath=path_to_saved_checkpoint --tlabels=path_to_train_labels --vlabels=path_to_val_labels --factors=both --attr_num=26 --experiment=PA-100K --device=cpu
   ```

4. Use `"create_compressed_model.py"` to create "Fully-Compressed" or "Partially-Compressed" Model.
   
   1. To create a Fully-Compressed Model:
      (Use create_compressed_model.py script)
      ```
      python3 create_compressed_model.py --model_type=full --save_path=path_to_save_model --ranks_dir=directory_to_load_factors&ranks --attr_num=26 or 35
      ```
   2. To create a Partially-Compressed Model:
      1. Perform step i to create full model.
      2. Use create_compressed_model.py script for partial model creation. <br />
           ```
           python3 create_compressed_model.py --full_model=path_to_full_model --model_type=partial --save_path=path_to_save_model --ranks_dir=directory_to_load_factors&ranks --attr_num=26 or 35
           ```

5. Use `"run_main.py"` to fine-tune compressed model <br />
   ``` 
   python3 run_main.py --model=COMPRESSED --attr_num=26 --experiment=PA-100K --epoch=15 --model_path=path_to_compressed_model --checkpoint_save=path_to_save_checkpoints
   ```

Besides,<br />
`layer_decomposer.py` can also be used to decompose specific layers. <br />

example:
```
python3 layer_decomposition.py --layer=main_branch.conv1_7x7_s2 --rank=81 --eps=0.002 --device=cpu --dpath=dataset_path --mpath=model_path --tlabels=data_train_label --vlabels=data_val/test_label --experiment=PA-100K --attr_num=26
```
Additionally,
1. The pretrained model for BNInception can be found here: https://www.dropbox.com/sh/z4cnegl1p1tsn8j/AADYxmDA5Of24vtjx3PK1o7Ba?dl=0
2. If there are problems installing flopco directly from pip. Clone the repo: `git clone https://github.com/juliagusak/flopco-pytorch.git`
`cd /flopco-pytorch` `mv flopco ../` 

Test Calibration using Binning:
```python
import numpy
from utils.binning import binning 
num_bins = 10
figsize = (4, 4)

binning(numpy.hstack(preds_original), numpy.hstack(labels), num_bins=num_bins, savename="calibrated_compressed_network.png", figsize=figsize, title="Histogram Binning_PA-100K(Compressed-Caliberated)")
```
If this work is helpful for your research, please consider citing the following BibTeX entry.

```bibtex
@article{jha2023lightweight,
  title={Lightweight Attribute Localizing Models for Pedestrian Attribute Recognition},
  author={Jha, Ashish and Ermilov, Dimitrii and Sobolev, Konstantin and Phan, Anh Huy and Ahmadi-Asl, Salman and Ahmed, Naveed and Junejo, Imran and Aghbari, Zaher AL and Baker, Thar and Khedr, Ahmed Mohamed and others},
  journal={arXiv preprint arXiv:2306.09822},
  year={2023}
}
```
