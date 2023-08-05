![Screenshot_from_2019-01-02_17-27-34 x15](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/c913f0dd-c8b7-48d4-aec4-ab8329fc2327)

## Kidney Tumor Segmentation


There are more than 400,000 new cases of kidney cancer each year , and surgery is its most common treatment. Due to the wide variety in kidney and kidney tumor morphology, there is currently great interest in how tumor morphology relates to surgical outcomes, [3,4] as well as in developing advanced surgical planning techniques. Automatic semantic segmentation is a promising tool for these efforts, but morphological heterogeneity makes it a difficult problem.

The goal of this challenge is to accelerate the development of reliable kidney and kidney tumor semantic segmentation methodologies. We have produced ground truth semantic segmentations for arterial phase abdominal CT scans of 300 unique kidney cancer patients who underwent partial or radical nephrectomy at our institution. 210 of these have been released for model training and validation, and the remaining 90 will be held out for objective model evaluation .


## Diagram

![MIScnn pipeline](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/a0bd0084-e7a3-485f-927f-3dec563aef8e)

## How to Use

### Downloading the Dataset
first utilizes `git lfs`.
Here is a brief run-down for Google Colaboratory:
```
! sudo add-apt-repository ppa:git-core/ppa
! curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
! sudo apt-get install git-lfs
! git lfs install
% cd "/content/"
! rm -r kits19
! git clone https://github.com/neheller/kits19.git
# takes roughly 11 minutes to download


```

This will download the much larger and static image files from a separate source. The `data/` directory should then be structured as follows
```
data
├── case_00000
|   ├── imaging.nii.gz
|   └── segmentation.nii.gz
├── case_00001
|   ├── imaging.nii.gz
|   └── segmentation.nii.gz
...
├── case_00209
|   ├── imaging.nii.gz
|   └── segmentation.nii.gz
└── kits.json
```

We've provided some basic Python scripts in `starter_code/` for loading and/or visualizing the data. 

### Loading Data

```python
from starter_code.utils import load_case

volume, segmentation = load_case("case_00123")
# or
volume, segmentation = load_case(123)
```

Will give you two `Nifty1Image`s. Their shapes will be `(num_slices, height, width)`, and their pixel datatypes will be `np.float32` and `np.uint8` respectively. In the segmentation, a value of 0 represents background, 1 represents kidney, and 2 represents tumor.

For information about using a `Nifty1Image`, see the [Nibabel Documentation](https://nipy.org/nibabel/manual.html#manual) ([Getting Started](https://nipy.org/nibabel/gettingstarted.html))

### Visualizing Data

The `visualize.py` file will dump a series of PNG files depicting a case's imaging with the segmentation label overlayed. By default, red represents kidney and blue represents tumor.

From Bash:

```bash
python3 starter_code/visualize.py -c case_00123 -d <destination>
# or
python3 starter_code/visualize.py -c 123 -d <destination>
```

From Python:

```python
from starter_code.visualize import visualize

visualize("case_00123", <destination (str)>)
# or
visualize(123, <destination (str)>)
```

![download](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/748467bf-8eda-44b9-9039-60f6cdf7690d)


### Train



## Evaluation

