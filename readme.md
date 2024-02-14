```
dvc init
wget https://code.dvc.org/get-started/code.zip
unzip code.zip && rm -f code.zip
dvc get https://github.com/iterative/dataset-registry \
          get-started/data.xml -o data/data.xml
conda create --name dvc python=3.10 -y
conda activate dvc
pip install -r src/requirements.txt
dvc stage add -n prepare \
                -p prepare.seed,prepare.split \
                -d src/prepare.py -d data/data.xml \
                -o data/prepared \
                python src/prepare.py data/data.xml
dvc stage add -n featurize \
                -p featurize.max_features,featurize.ngrams \
                -d src/featurization.py -d data/prepared \
                -o data/features \
                python src/featurization.py data/prepared data/features
dvc stage add -n train \
                -p train.seed,train.n_est,train.min_split \
                -d src/train.py -d data/features \
                -o model.pkl \
                python src/train.py data/features model.pkl
dvc repro
dvc dag
```