# Polyp segmentation: a controlled federated study

Code: CVC_updated_final_journal_17_OCTOBER_with_outputs_filtered.ipynb and Kvasir SEG - updated 17 october
(two  notebook; results, tables and figures are saved in its cell outputs)

## Data
The datasets (Kvasir-SEG, CVC-ClinicDB, CVC-300, CVC-ColonDB, ETIS) are not included in this repository.
We use the PraNet training/test split. Download TrainDataset and TestDataset from the official PraNet page
[(https://github.com/DengPingFan/PraNet)](https://github.com/DengPingFan/PraNet) 
and place them in a folder named PraNet_data next to the notebook.
All datasets belong to their original authors; please follow their licences.

## Split lists

split_image_ids.csv lists the training, validation and test image IDs used in our experiments.



## Contact
Eman Majeed / F2024393035@umt.edu.pk


## Configuration and seeds for CVC clinic db

- Training seeds: 42, 123, 2024, 7, 99 (5 seeds). Split seed: 42 (validation = random 10% of each training dataset).
- Model: plain ResNet-50 U-Net, ImageNet-initialized, full fine-tuning. Input 352 x 352, batch size 8, Adam, learning rate 1e-4.
- Loss: BCE + Dice. Checkpoint: best validation Dice. Threshold: 0.5. No early stopping.
- Epochs: 40 for every configuration (local-only, pooled, ablations).
- Federated learning: 2 clients (Kvasir-SEG, CVC-ClinicDB), 20 rounds x 2 local epochs (= 40 epochs), FedAvg, FedProx (mu = 0.01), FedBN. No warm start.
- XAI: Grad-CAM++ on layer dec4_relu2; LIME with 150 segments x 1000 samples.
- Near-duplicate check: perceptual hash, Hamming distance <= 8.
- Hardware: 1 x NVIDIA GeForce RTX 3080 (10 GB).

## Environment
Python 3.8.10, TensorFlow 2.12.0, NumPy 1.24.4, SciPy 1.10.1, pandas 2.0.3, matplotlib 3.7.1,
scikit-learn 1.3.2, scikit-image 0.21.0, OpenCV (cv2 5.0.0 as reported by the notebook), h5py 3.8.0.


## Configuration and seeds (Kvasir-SEG notebook)

- Training seeds: 42, 123, 2024, 7, 99 (5 seeds). Split seed: 42 (validation = random 10% of the Kvasir-SEG training images).
- Data: PraNet protocol. Only Kvasir-SEG is used for training (810 train / 90 validation images).
  Test sets: Kvasir-SEG (100), CVC-ClinicDB (62), CVC-300 (60), CVC-ColonDB (380), ETIS (196).
- Main model: plain ResNet-50 U-Net, ImageNet-initialized, full fine-tuning. Input 352 x 352, batch size 8, Adam, learning rate 1e-4,
  ReduceLROnPlateau on validation Dice, loss = 0.5 * BCE + 0.5 * (1 - Dice). Checkpoint: best validation Dice. Threshold 0.5. No early stopping.
- Epochs: 40 for every configuration (main model and all ablations), so the training budget is matched.
- Ablations: frozen encoder; no ImageNet initialization; CLAHE preprocessing; complex architecture
  (ResNet-50 + ASPP + attention-gated decoder + CBAM) and its variants without CBAM and without attention gates.
- Inference-time options (4-way flip TTA, largest-component post-processing, threshold tuned on validation) are evaluated separately
  on the same trained weights, without retraining. The default evaluation uses none of them.
- XAI: Grad-CAM++ on layer dec4_relu2 (100 test images); LIME with 150 segments x 1000 samples (50 images).
- Near-duplicate check: perceptual hash, Hamming distance <= 8.
- Hardware: 1 x NVIDIA GeForce RTX 3080 (10 GB).

## Environment
Python 3.8.10, TensorFlow 2.12.0, NumPy 1.24.4, SciPy 1.10.1, pandas 2.0.3, matplotlib 3.7.1,
scikit-learn 1.3.2, scikit-image 0.21.0, OpenCV (cv2 5.0.0 as reported by the notebook), h5py 3.8.0, tqdm 4.67.3.
