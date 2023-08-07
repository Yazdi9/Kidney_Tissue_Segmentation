![Screenshot_from_2019-01-02_17-27-34 x15](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/c913f0dd-c8b7-48d4-aec4-ab8329fc2327)

## Kidney Tumor Segmentation


There are more than 400,000 new cases of kidney cancer each year , and surgery is its most common treatment. Due to the wide variety in kidney and kidney tumor morphology, there is currently great interest in how tumor morphology relates to surgical outcomes,  as well as in developing advanced surgical planning techniques. Automatic semantic segmentation is a promising tool for these efforts, but morphological heterogeneity makes it a difficult problem.

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


## pipeline

```python
# Import the MIScnn module
import miscnn

# Create a Data I/O interface for kidney tumor CT scans in NIfTI format
from miscnn.data_loading.interfaces import NIFTI_interface
interface = NIFTI_interface(pattern="case_000[0-9]*", channels=1, classes=3)

# Initialize data path and create the Data I/O instance
data_path = "/kits19/data/"
data_io = miscnn.Data_IO(interface, data_path)

# Create a Preprocessor instance to configure how to preprocess the data into batches
pp = miscnn.Preprocessor(data_io, batch_size=4, analysis="patchwise-crop",
                         patch_shape=(128,128,128))

# Create a deep learning neural network model with a standard U-Net architecture
from miscnn.neural_network.architecture.unet.standard import Architecture
unet_standard = Architecture()
model = miscnn.Neural_Network(preprocessor=pp, architecture=unet_standard)
```

## Train

Let's run a model training on our data set. Afterwards, predict the segmentation of a sample using the fitted model.

```python
# Training the model with 80 samples for 500 epochs
sample_list = data_io.get_indiceslist()
model.train(sample_list[0:80], epochs=500)

# Predict the segmentation for 20 samples
pred = model.predict(sample_list[80:100], return_output=True)
```

## Evaluation

Now, let's run a 5-fold Cross-Validation with our model, create automatically evaluation figures and save the results into the directory "evaluation_results".

```python
from miscnn.evaluation import cross_validation

cross_validation(sample_list, model, k_fold=5, epochs=100,
                 evaluation_path="evaluation_results", draw_figures=True)
```
## 

<table class="center">
<tr>
  <td style="text-align:center;"><b>Validation Dice Soft</b></td>
  <td style="text-align:center;"><b>Validation Loss</b></td>
   <td style="text-align:center;"><b>Validation Dice Cross Entropy</b></td>
</tr>
  
<tr>
 <td>

![download (1)](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/800fd950-537c-4fcc-a5ad-e87167fdd719)



</td>
  <td>
  

![download (2)](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/bc059a31-23f7-405c-a1ce-7066d61ff157)


  </td>
  <td>

![download (6)](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/7762d2ea-2bf3-4a22-aa75-25aa3358d2f8)


</td> 

</tr> 

</td>

</table>


##  Results

<table>


<tr>


<th style="width: 250px;">Fold 1</th>

<th>Fold 0</th>

</tr>

<tr>



<td><img src="https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/f6c90491-3012-4a62-b766-e4c042facba4.gif"  width="385"></td>

<td><img src="https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/d4b7b85b-5a68-4c6f-9476-967bbfa87f4d.gif"  width="385"> </td>

</tr>

<tr>


<td><img src="https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/212e39b2-8002-41b4-b527-389275ace5c1.gif"  width="385"></th>

<td><img src="https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/10fff419-cbf4-45cf-8236-8e4529fb0caa.gif"  width="385"></th>

</tr>



</table>



![Screenshot (5066)](https://github.com/saba99/Kidney_Tissue_Segmentation/assets/33378412/17bf30b3-cac2-4917-b824-708bff4beddc)

