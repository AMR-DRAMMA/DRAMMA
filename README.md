# DRAMMA: Detection of Resistance to AntiMicrobials using Machine-learning Approaches

## Summary

Antibiotics are essential for medical procedures, food security, and public health. However, ill-advised usage leads to increased pathogen resistance to antimicrobial substances, posing a threat of fatal infections and limiting the benefits of antibiotics. Therefore, early detection of antimicrobial resistance genes (ARGs), especially in human pathogens, is crucial.

DRAMMA (Detection of Resistance to AntiMicrobials  using Machine-learning Approaches) is a multifaceted machine-learning approach for novel antimicrobial resistance gene detection in metagenomic data. Unlike most existing methods that rely on sequence similarity to a predefined gene database, DRAMMA can predict new ARGs with no sequence similarity to known resistance or even annotated genes.

## Features

- Utilizes various features including protein properties, genomic context, and evolutionary patterns
- Demonstrates robust performance in cross-validation and external validation
- Enables rapid ARG identification for large-scale genetic and metagenomic samples
- Potential for early detection of specific ARGs, influencing antibiotic selection for patients

## Installation

1. Clone the repository:
   ```
   git clone https://github.com/AMR-DRAMMA/DRAMMA.git
   cd DRAMMA
   ```

2. Create a conda environment (optional but recommended):
   ```
   conda env create -f environment.yml
   conda activate DRAMMA
   ```

### Requirements

### Python and Libraries
- Python 3.10+
- pandas
- numpy
- scikit-learn
- scipy
- matplotlib
- seaborn
- Bio
- mpmath

### External Programs
- HMMER version 3.2.1
- TMHMM 2.0c
- MMseqs2

Please ensure these external programs are installed and accessible in your system's PATH, or provide their full paths when running the scripts.


## Getting the data
Downloading the trained models and data needed for the feature extraction scripts.

In order to get the trained models, click on the following link [http://tiny.cc/models_tgz](http://tiny.cc/models_tgz)
or use the command line:
```
cd data
wget http://tiny.cc/models_tgz
tar -zxvf models.tar.gz
rm models.tar.gz
```
To get the data needed for the feature extraction scripts, click on the following link [http://tiny.cc/data_tgz](http://tiny.cc/data_tgz) or use the command line:
```
cd data
wget http://tiny.cc/data_tgz
tar -zxvf data.tar.gz
rm data.tar.gz
```
## Usage

The main scripts for DRAMMA are:

### 1. run_DRAMMA_pipeline.py

This script executes all the steps needed to use the trained DRAMMA model on a given data: feature extraction, dataset creation out of all the samples' features, and applying the model on the dataset. Returns the label according to our ARG HMM DB, model probability score, and whether it passed the relevant model score thresholds (0.75 and 0.95, where 0.95 is more strict).
The script assumes there are four files for each assembly - protein fasta, genes, gff file, and contig file (faa, ffn, gff, fa).

```
python run_DRAMMA_pipeline.py <input_path> --data_path <data_path> -out <output_path> --hmmer_path <path_to_hmmer> --mmseqs_path <path_to_mmseqs> --tmhmm_path <path_to_tmhmm> -pkl <path_to_model_pickle> [options]

Options:
  --input_path          Full path of the directory with all the assemblies. Not needed if --dif_format_paths is supplied.
  --dif_format_paths    Paths to data in different formats (faa, fa, gff, ffn) (optional, use only if you want to extract the features on a single assembly.)
  --data_path           Full path of the directory with all the data needed for feature extraction (feature_extraction directory downloaded from the link above)
  --hmmer_path          Full path to the HMMER's hmmsearch program
  --mmseqs_path         Full path to the Mmseqs2 program
  --tmhmm_path          Full path to the tmhmm program
  --feature_dir         Full path to the directory we want to save our features in (default: "features", new subdirectory of the current directory)
  -k, --kmer            Run kmers count from 2 to k (default: 4)
  -lt, --label_threshold Threshold for the proximity feature (default: '1e-10')
  -t, --threshold_list  List of thresholds for proximity feature (default: [1e-8])
  -d, --gene_window     Size of the ORFs window (default: 10)
  -n, --nucleotide_window Size of the nucleotides window (default: 10000)
  -sf, --suffix         suffix to sample files such that the protein file will end with {suffix}proteins.faa. for example, .min10k. to get only contigs of length more than 10k. Input '' (default value) if none applies
  -ftd, --features_to_drop List of features to exclude (default: ['Cross_Membrane'])
  -b, --batch_size      batch size for saving the dataset when the script is run on a directory of multiple samples(default: 0, everything will be saved in a single file)
  -pkl, --pickle        Path to pickle with the model, relevant cols, and model score thresholds dictionary (created by create_model_pkl.py or downloaded from the link above)
  -sc, --single_class   Choose this to run a binary model (default)
  -mc, --multi_class    Choose this to run a multi_class model
  -out, --output_file   Path to pkl file we want to save our results in
  --keep_files          keep all feature files
  --remove_files        remove all feature files (default)
```

### 2. run_features.py

This script extracts features from input data. The script assumes there are four files for each assembly - protein fasta, genes, gff file, and contig file (faa, ffn, gff, fa).

```
python run_features.py --input_path <input_path> --data_path <data_path> --hmmer_path <path_to_hmmer> --mmseqs_path <path_to_mmseqs> --tmhmm_path <path_to_tmhmm> [options]

Options:
  --input_path          Full path of the directory with all the assemblies. Not needed if --dif_format_paths is supplied.
  --dif_format_paths    Paths to data in different formats (faa, fa, gff, ffn) (optional, use only if you want to extract the features on a single assembly.)
  --output_dir          Full path to the directory we want to save our features in (default: "features", new subdirectory of the current directory)
  --data_path           Full path of the directory with all the data needed for feature extraction (feature_extraction directory downloaded from the link above)
  --hmmer_path          Full path to the HMMER's hmmsearch program
  --mmseqs_path         Full path to the Mmseqs2 program
  --tmhmm_path          Full path to the tmhmm program
  -k, --kmer            Run kmers count from 2 to k (default: 4)
  -lt, --label_threshold Threshold for the proximity feature (default: '1e-10')
  -t, --threshold_list  List of thresholds for proximity feature (default: [1e-8])
  -d, --gene_window     Size of the ORFs window (default: 10)
  -n, --nucleotide_window Size of the nucleotides window (default: 10000)
  -e, --by_evalue       Use threshold by e-value (default)
  -s, --by_score        Use threshold by score
  -sf, --suffix         suffix to sample files such that the protein file will end with {suffix}proteins.faa. for example, .min10k. (default value) to get only contigs of length more than 10k. Input '' if none applies
  -ftd, --features_to_drop List of features to exclude (default: ['Cross_Membrane'])
  -pkl, --pickle_file   Path to pickle file with a FeatureList object (optional, if its not supplied, a new object will be created)
```

### 3. train_dataset_creator.py

This script creates a dataset for training the model, either a balanced subset of the proteins or the complete dataset.
```
python train_dataset_creator.py -d <directory> -f <fasta_file> [options]
Options:
-d, --directory       Directory containing all the feature pkl files created by run_features.py
-f, --fasta           Path to relevant protein fasta file for de-duplication. only used when all_data is false.
-wl, --whitelist      Filter which folders to check (default: '' - checks all files and directories in --directory)
-p, --pumps           Create the pump train set (default: False)
-ad, --all_data       Create dataset on entire data instead of balanced set (default: False)
-pkl, --pickle        Save data to pickle instead of tsv (default: True)
-b, --batch_size      Batch size for saving dataset when all_data=True (default: 0 - everything in one file)
-c, --columns         JSON file with the columns to include in the dataset (default: '' - use all columns)
```

### 4. create_model_pkl.py

This script creates a pickle file with all the relevant information for running the model (model, relevant cols, and model score thresholds dictionary).

```
python create_model_pkl.py -i <input_path> -o <output_path> [options]

Options:
  -i, --input_path      Path to input dataset
  -o, --output_path     Path to output pickle
  -nj, --n_jobs         Number of jobs to use to train the model (default: 2)
  -ts, --test_set       Path to the test set for model evaluation (optional)
  --param_pkl           Path to pickle with chosen model hyperparameters (optional)
  --feature_json        Path to JSON file with the chosen features. (optional, default is the parameters used to train DRAMMA. Choose '' for the default Random forest hyperparameters)
  -n                    Number of top features to choose (default: 30, choose 0 to use all not correlated features)
  -mc, --is_multiclass  Create a model for the multiclass classifier (default: False)
  -lc, --label_col      Column to take for labeling (default: 'Updated Resistance Mechanism')
```

### 5. run_model.py

This script runs the trained model on input data.

```
python run_model.py -pkl <path_to_model_pickle> -in <input_file> -out <output_file> [options]

Options:
  -pkl, --pickle        Path to pickle with the model, relevant cols, and model score thresholds dictionary (created by create_model_pkl.py or downloaded from the link above)
  -in, --input_file     Path to the file we want to run the model against
  -out, --output_file   Path to pkl file we want to save our results in
  -fp, --filter_pos     Choose this to keep only negative proteins (non-AMRs) (default)
  -kp, --keep_pos       Choose this to keep both positive (known AMRs) and negative proteins (non-AMRs)
  -fl, --filter_low_scores   Choose this to only keep proteins that passed the minimal model score according to the model score thresholds dictionary (default)
  -kl, --keep_low_scores    Choose this to keep the results of proteins that received low model score as well
  -sc, --single_class   Choose this to run a binary model (default)
  -mc, --multi_class    Choose this to run a multi_class model
```


## Contact
For questions about DRAMMA, please contact us:
[amr.dramma@gmail.com](mailto:amr.dramma@gmail.com)
<!--

## Citation

If you use DRAMMA in your research, please cite our paper:

(Add citation information here when available)

-->