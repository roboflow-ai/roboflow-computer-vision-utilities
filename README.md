# roboflow-computer-vision-utilities
Interface with the Roboflow API and Python package for running inference (receiving predictions) from your Roboflow Train computer vision models.

![Roboflow Logo](https://camo.githubusercontent.com/b9468c9d506b644007e50189fd2aa5d5f158b992bb21569222fe3967e608c467/68747470733a2f2f692e696d6775722e636f6d2f6c58436f5674352e706e67)

# Tell us about what you built, ask us questions, or provide feedback for improvements! opensource@roboflow.com and our [Community Forum](https://discuss.roboflow.com):
## [Website](https://docs.roboflow.com/python) • [Docs](https://docs.roboflow.com) • [Blog](https://blog.roboflow.com) • [Twitter](https://twitter.com/roboflow) • [Linkedin](https://www.linkedin.com/company/roboflow-ai) • [Roboflow Universe](https://universe.roboflow.com) • [Knowledge Base](https://help.roboflow.com)

# What is Roboflow?
## **Roboflow** makes managing, preprocessing, augmenting, and versioning datasets for computer vision seamless. This repo utilizes
## the official [Roboflow python package](https://docs.roboflow.com/python) that interfaces with the [Roboflow API](https://docs.roboflow.com/inference/hosted-api). Key features of Roboflow:

- Import and Export image datasets into any supported [formats](https://roboflow.com/formats)
- [Preprocess](https://docs.roboflow.com/image-transformations/image-preprocessing)
  and [augment](https://docs.roboflow.com/image-transformations/image-augmentation) data using Roboflow's dataset
  management tools
- Train computer vision models using [Roboflow Train](https://docs.roboflow.com/train) and deploy
  to [production](https://docs.roboflow.com/inference)
- Use [community curated projects](https://universe.roboflow.com/) to start building your own vision-powered products

## [Features by workspace type](https://roboflow.com/pricing)

# Personal and Research Projects - Applying for Additional Account Features:
* https://roboflow.com/community

# Business Projects and POC's - Requesting Additional Account Features:
* https://roboflow.com/sales

# GitHub Repo Structure:
* The `Images` directory contains necessary code for running inference (model predictions) on individual images, and folders (directories) of images
* `predictionUtils.py` is written in an Object-Oriented Programming framework, and contains necessary code for interacting with `Images.py` for saving [customized] images from inference results.

[In Progress]:
* `Video.py`: Code for running inference (model predictions) on local video files
* `Webcam.py`: Code for running inference (model predictions) on webcam feeds

## Installation (Dependencies):

To install the Python package, please use `Python 3.6` or higher. We provide three different ways to install the Roboflow
package to use within your own projects.

Install from Source:

```
git clone https://github.com/roboflow-ai/roboflow-computer-vision-utilities.git
cd roboflow-computer-vision-utilities
python3 -m venv env
source env/bin/activate 
pip3 install -r requirements.txt
```

## [Obtaining Your API Key](https://docs.roboflow.com/rest-api#obtaining-your-api-key) | [Locating Project Information](https://docs.roboflow.com/python#finding-your-project-information-manually)

Colab Tutorials Here:
<a href="https://colab.research.google.com/drive/1UxQTtSqxUF2EM-iS0j7hQPFdqC0eW66A?usp=sharing"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"></a>
- Select "File" in the Google Colab menu, and "Save a Copy in Drive" prior to running the notebook

![Saving Your Own Copy of the Colab Notebook](/figures/ColabNotebook_SaveFile.png)

![The Roboflow API JSON Response Object Format](/figures/ResponseObjectFormat_JSON.png)

![Visualized Roboflow API JSON Response Object Format](/figures/Visualized_ResponseObjectFormat_JSON.png)

### Drawing Bounding Boxes
```
# main bounding box coordinates from JSON response object
# https://docs.roboflow.com/inference/hosted-api#response-object-format
x0 = bounding_box['x'] - bounding_box['width'] / 2
x1 = bounding_box['x'] + bounding_box['width'] / 2
y0 = bounding_box['y'] - bounding_box['height'] / 2
y1 = bounding_box['y'] + bounding_box['height'] / 2

# position coordinates: start = (x0, y0), end = (x1, y1)
# color = RGB-value for bounding box color, (0,0,0) is "black"
# thickness = stroke width/thickness of bounding box
# draw and place bounding boxes
start_point = (int(x0), int(y0))
end_point = (int(x1), int(y1))
cv2.rectangle(img, start_point, end_point, color=(0,0,0), thickness=2)
```

### Drawing "Filled" Bounding Boxes
```
# main bounding box coordinates from JSON response object
# https://docs.roboflow.com/inference/hosted-api#response-object-format
x0 = bounding_box['x'] - bounding_box['width'] / 2
x1 = bounding_box['x'] + bounding_box['width'] / 2
y0 = bounding_box['y'] - bounding_box['height'] / 2
y1 = bounding_box['y'] + bounding_box['height'] / 2

# position coordinates: start = (x0, y0), end = (x1, y1)
# color = RGB-value for bounding box color, (0,0,0) is "black"
# thickness = stroke width/thickness of bounding box
# draw and place bounding boxes
start_point = (int(x0), int(y0))
end_point = (int(x1), int(y1))
# setting thickness to -1 --> filled bounding box with the specified color
cv2.rectangle(img, start_point, end_point, color=(0,0,0), thickness=-1)
```

### Blurring the Contents of Bounding Boxes
```
# rip bounding box coordinates from current detection
# note: infer returns center points of box as (x,y) and width, height
# ----- but pillow crop requires the top left and bottom right points to crop
x0 = prediction['x'] - prediction['width'] / 2
x1 = prediction['x'] + prediction['width'] / 2
y0 = prediction['y'] - prediction['height'] / 2
y1 = prediction['y'] + prediction['height'] / 2
box = [(x0, y0), (x1, y1)]
blur_x = int(prediction['x'] - prediction['width'] / 2)
blur_y = int(prediction['y'] - prediction['height'] / 2)
blur_width = int(prediction['width'])
blur_height = int(prediction['height'])
# region of interest (ROI), or area to blur
roi = img[blur_y:blur_y+blur_height, blur_x:blur_x+blur_width]

# ADD BLURRED BBOXES
# set blur to (31,31) or (51,51) based on amount of blur desired
blur_image = cv2.GaussianBlur(roi,(51,51),0)
img[blur_y:blur_y+blur_height, blur_x:blur_x+blur_width] = blur_image
```

### Writing and Placing Text
```
# write and place text
cv2.putText(
    img, # PIL.Image object to place text on
    'placeholder text',#text to place on image
    (12, 12),#location of text in pixels
    font = cv2.FONT_HERSHEY_SIMPLEX, #text font
    0.6,#font scale
    (0, 0, 0),#text color in RGB
    thickness=2#thickness/"weight" of text
    )
```

### Run model inference on a single image file:
```
# perform inference on the selected local image file
file_location = "YOUR_IMAGE.jpg"
predictions = model.predict(file_location, confidence=40, overlap=30)
# save prediction image
predictions.save(f'inferenceResult_{os.path.basename(img_file)}')
predictions_json = predictions.json()
print(predictions_json)

# perform inference on the selected HOSTED image file
file_location = "https://www.yourimageurl.com"
prediction_hosted = model.predict(file_location, confidence=40,overlap=30, hosted=True)
# save prediction image
predictions_hosted.save(f'inferenceResult_{file_location.split('www.')[1]}')
predictions_hosted_json = predictions_hosted.json()
print(predictions_hosted_json)
```
### Run model inference on a folder (directory) of image files:
```
raw_data_location = "INSERT_PATH_TO_IMG_DIRECTORY"
for raw_data_extension in ['.jpg', '.jpeg', 'png']:
## using the following line for raw_data_externsion results in inference on
## specified file types only
# raw_data_extension = ".jpg" # e.g jpg, jpeg, png
    globbed_files = glob.glob(raw_data_location + '/*' + raw_data_extension)
    for img_file in globbed_files:
        predictions = model.predict(img_file, confidence=40, overlap=30)
        # save prediction image
        predictions.save(f'inferenceResult_{os.path.basename(img_file)}')
        predictions_json = predictions.json()
        print(predictions_json)
```