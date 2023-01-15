{
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/filispeen/Meshroom-colab.ipynb/blob/main/Meshroom_colab.ipynb\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "# Setup\n",
        "* **`alicevision_version`**: Version alicevion/meshroom used to work with photos\n",
        "\n",
        "---\n",
        "\n",
        "* **`upload_type`** : Select the type of upload method.\n",
        "\n",
        "* **`photo_archive_path`** : Path to your zip file relative to the root of your Google Drive if google_drive is selected. Must state the file and its extension (.zip) unless gdrive_relative is selected.\n",
        "\n",
        "* **`url_photo_zip_url`** : Specify the URL to the blend/zip file if url is selected.\n",
        "\n",
        "---\n",
        "\n",
        "* **`download_type`** : Select the type of download method. **`gdrive_direct`** enables the archive to be outputted directly to Google Drive.\n",
        "\n",
        "* **`drive_output_path`** : Path to your zip file in Google Drive.\n",
        "\n",
        "After you are done, go to **Runtime > Run All (Ctrl + F9)** and upload your files or have Google Drive authorised below. See the GitHub repo for more information."
      ],
      "metadata": {
        "id": "Eeyl5jsmoxY5"
      }
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "KBdo678n82S8",
        "cellView": "form"
      },
      "outputs": [],
      "source": [
        "alicevision_version = \"2021.1.0\" #@param [\"2021.1.0\", \"2020.1.1\", \"2019.2.0\", \"2019.1.0\", \"2018.1.0\"]\n",
        "#@markdown ---\n",
        "upload_type = \"google_drive\" #@param [\"direct\", \"google_drive\", \"url\"]\n",
        "photo_archive_path = \"Meshroom/\\u003Czip-only>.zip\" #@param {type:\"string\"}\n",
        "url_photo_zip_url = \"https://site.com/file.zip\" #@param {type:\"string\"}\n",
        "#@markdown ---\n",
        "download_type = \"google_drive\" #@param [\"direct\", \"google_drive\"]\n",
        "drive_output_path = \"meshroomoutput\" #@param {type:\"string\"}\n",
        "\n"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "Ev1xzLiWFE-j"
      },
      "outputs": [],
      "source": [
        "#@title Checking GPU { vertical-output: true, display-mode: \"form\" }\n",
        "import os\n",
        "\n",
        "!ls\n",
        "\n",
        "!LD_LIBRARY_PATH=/usr/lib/nvidia:$LD_LIBRARY_PATH\n",
        "\n",
        "gpu = !nvidia-smi --query-gpu=gpu_name --format=csv,noheader\n",
        "print(\"Current GPU: \" + gpu[0])"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "10PJrrufC_Tc"
      },
      "outputs": [],
      "source": [
        "#@title Downloading archive { vertical-output: true, display-mode: \"form\" }\n",
        "import shutil\n",
        "import os\n",
        "from google.colab import files, drive\n",
        "\n",
        "!apt install unzip\n",
        "\n",
        "uploaded_filename = \"\"\n",
        "\n",
        "if upload_type == 'google_drive' or upload_type == 'gdrive_relative' or download_type == 'google_drive' or download_type == 'gdrive_direct':\n",
        "    drive.mount('/drive')\n",
        "\n",
        "if upload_type == 'direct':\n",
        "    uploaded = files.upload()\n",
        "    for fn in uploaded.keys():\n",
        "        uploaded_filename = fn\n",
        "elif upload_type == 'url':\n",
        "    !wget -O content.zip -nc $url_photo_zip_url\n",
        "    uploaded_filename = os.path.basename(url_photo_zip_url)\n",
        "elif upload_type == 'google_drive':\n",
        "    !rm -r content.zip\n",
        "    shutil.copy('/drive/My Drive/' + photo_archive_path, \".\")\n",
        "    !mv *.zip content.zip \n",
        "    uploaded_filename = os.path.basename(photo_archive_path)"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "VaD3-cKw-qqk"
      },
      "outputs": [],
      "source": [
        "#@title Downloading meshroom { vertical-output: true, display-mode: \"form\" }\n",
        "alicevision_url_dict = {'2021.1.0' : \"https://github.com/alicevision/Meshroom/releases/download/v2021.1.0/Meshroom-2021.1.0-linux-cuda10.tar.gz\",\n",
        "                        '2020.1.1' : \"https://github.com/alicevision/Meshroom/releases/download/v2020.1.1/Meshroom-2020.1.0-linux-cuda10.tar.gz\",\n",
        "                        '2019.2.0' : \"https://github.com/alicevision/Meshroom/releases/download/v2019.2.0/Meshroom-2019.2.0-linux.tar.gz\",\n",
        "                        '2019.1.0' : \"https://github.com/alicevision/Meshroom/releases/download/v2019.1.0/Meshroom-2019.1.0-linux.tar.gz\",\n",
        "                        '2018.1.0' : \"https://github.com/alicevision/Meshroom/releases/download/v2018.1.0/Meshroom-2018.1.0-linux.tar.gz\"}\n",
        "\n",
        "alicevision_url = alicevision_url_dict[alicevision_version]\n",
        "base_url = os.path.basename(alicevision_url)\n",
        "\n",
        "!rm -r $alicevision_version\n",
        "!mkdir $alicevision_version\n",
        "!wget -nc $alicevision_url\n",
        "!tar -xkvf $base_url -C ./$alicevision_version --strip-components=1"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "NiAvwC4xQ6d5"
      },
      "outputs": [],
      "source": [
        "#@title Meshroom starting work { vertical-output: true, display-mode: \"form\" }\n",
        "%cd\n",
        "\n",
        "%cd ../content\n",
        "\n",
        "!rm -r $alicevision_version/content/*\n",
        "\n",
        "!unzip content.zip -d $alicevision_version/content\n",
        "\n",
        "%cd $alicevision_version/\n",
        "\n",
        "!ls\n",
        "\n",
        "!rm -r output\n",
        "\n",
        "!mkdir output\n",
        "\n",
        "!./meshroom_batch --input content --output output\n",
        "\n",
        "#!bash aliceVision_cameraInit-2.0 --imageFolder \"./content/content/\" --sensorDatabase \"./content/content/cameraSensors.db\" --defaultFieldOfView 45.0 --groupCameraFallback folder --allowedCameraModels pinhole,radial1,radial3,brown,fisheye4,fisheye1 --useInternalWhiteBalance True --viewIdMethod metadata --verboseLevel info --output \"./content/content/cameraInit.sfm\" --allowSingleView 1 --input \"./content/content//viewpoints.sfm\"\n",
        "\n",
        "#!bash aliceVision_featureExtraction  --input \"./content/content/cameraInit.sfm\" --describerTypes sift --describerPreset normal --describerQuality normal --contrastFiltering GridSort --gridFiltering True --forceCpuExtraction True --maxThreads 0 --verboseLevel info --output \"./content/content/FeatureExtraction\" --rangeStart 0 --rangeSize 0\n",
        "\n",
        "#!bash aliceVision_featureExtraction  --input \"./content/content/cameraInit.sfm\" --describerTypes sift --describerPreset normal --describerQuality normal --contrastFiltering GridSort --gridFiltering True --forceCpuExtraction True --maxThreads 0 --verboseLevel info --output \"./content/content/FeatureExtraction\" --rangeStart 0 --rangeSize 40\n",
        "\n",
        "#!bash aliceVision_featureExtraction  --input \"./content/content/cameraInit.sfm\" --describerTypes sift --describerPreset normal --describerQuality normal --contrastFiltering GridSort --gridFiltering True --forceCpuExtraction True --maxThreads 0 --verboseLevel info --output \"./content/content/FeatureExtraction\" --rangeStart 0 --rangeSize 80\n",
        "\n",
        "#!bash aliceVision_imageMatching  --input \"./content/content/cameraInit.sfm\" --featuresFolders \"./content/content/FeatureExtraction\" --method VocabularyTree --tree \"./Meshroom-2021.1.0-linux-cuda10/aliceVision/share/aliceVision/vlfeat_K80L3.SIFT.tree\" --weights \"\" --minNbImages 200 --maxDescriptors 500 --nbMatches 50 --verboseLevel info --output \"./content/content/imageMatches.txt\"\n",
        "\n",
        "#!bash aliceVision_imageMatching  --input \"./content/content/cameraInit.sfm\" --featuresFolders \"./content/content/FeatureExtraction\" --method VocabularyTree --tree \"./Meshroom-2021.1.0-linux-cuda10/aliceVision/share/aliceVision/vlfeat_K80L3.SIFT.tree\" --weights \"\" --minNbImages 200 --maxDescriptors 500 --nbMatches 50 --verboseLevel info --output \"./content/content/imageMatches.txt\"\n",
        "\n",
        "#!bash aliceVision_featureMatching-2.0  --input \"./content/content/cameraInit.sfm\" --featuresFolders \"./content/content/\" --imagePairsList \"./content/content/imageMatches.txt\" --describerTypes sift --photometricMatchingMethod ANN_L2 --geometricEstimator acransac --geometricFilterType fundamental_matrix --distanceRatio 0.8 --maxIteration 2048 --geometricError 0.0 --knownPosesGeometricErrorMax 5.0 --maxMatches 0 --savePutativeMatches False --crossMatching False --guidedMatching False --matchFromKnownCameraPoses False --exportDebugFiles False --verboseLevel info --output \"./content/\" --rangeStart 0 --rangeSize 20\n"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#@title Making archive and importing on google drive/making download link for you { vertical-output: true, display-mode: \"form\" }\n",
        "output_folder_name = \"output\"\n",
        "\n",
        "if download_type == 'google_drive':\n",
        "  shutil.make_archive(output_folder_name, 'zip', 'output')\n",
        "  shutil.copy(output_folder_name + '.zip', '/drive/My Drive/' + drive_output_path)\n",
        "\n",
        "if download_type == 'direct':\n",
        "  shutil.make_archive(output_folder_name, 'zip', 'output')\n",
        "  files.download(output_folder_name + '.zip')\n",
        "\n",
        "raise SystemExit(\"See ya later! Script by @FILISPEEN\")\n",
        "\n",
        "\n"
      ],
      "metadata": {
        "id": "9CgNpOeuPzZa"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "# Disclaimer\n",
        "Google Colab is targeted to researchers and students to run AI/ML tasks, data analysis and education, not rendering 3D scenes. Because the computing power provided are free, the usage limits, idle timeouts and speed of the rendering may varies time by time. [Colab Pro and Colab Pro+](https://colab.research.google.com/signup) are available for those who wanted to have more powerful GPU and longer runtimes for rendering. See the [FAQ](https://research.google.com/colaboratory/faq.html) for more info. In some cases, it might be faster to use an online Blender renderfarm.\n",
        "\n",
        "# License\n",
        "```\n",
        "MIT License\n",
        "\n",
        "Copyright (c) 2020-2022 ynshung\n",
        "\n",
        "Permission is hereby granted, free of charge, to any person obtaining a copy\n",
        "of this software and associated documentation files (the \"Software\"), to deal\n",
        "in the Software without restriction, including without limitation the rights\n",
        "to use, copy, modify, merge, publish, distribute, sublicense, and/or sell\n",
        "copies of the Software, and to permit persons to whom the Software is\n",
        "furnished to do so, subject to the following conditions:\n",
        "\n",
        "The above copyright notice and this permission notice shall be included in all\n",
        "copies or substantial portions of the Software.\n",
        "\n",
        "THE SOFTWARE IS PROVIDED \"AS IS\", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR\n",
        "IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,\n",
        "FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE\n",
        "AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER\n",
        "LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,\n",
        "OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE\n",
        "SOFTWARE.\n",
        "```\n",
        "\n",
        "\n",
        "\n",
        "\n",
        "\n"
      ],
      "metadata": {
        "id": "Com2cKRSp6Tt"
      }
    }
  ],
  "metadata": {
    "colab": {
      "provenance": [],
      "toc_visible": true,
      "authorship_tag": "ABX9TyMnG7FcRshpa5cLrKeCpnnK",
      "include_colab_link": true
    },
    "kernelspec": {
      "display_name": "Python 3",
      "name": "python3"
    },
    "language_info": {
      "name": "python"
    },
    "accelerator": "GPU",
    "gpuClass": "standard"
  },
  "nbformat": 4,
  "nbformat_minor": 0
}
