# ImageJ Particle Analysis Macros

A collection of ImageJ macros for batch-processing confocal microscopy Z-stacks and quantifying fluorescent POS particles.

This repository includes two related workflows:

1. **Whole-stack particle analysis**, which quantifies particles across a maximum-intensity projection of the complete Z-stack.
2. **Apical/basal particle analysis**, which uses ZO-1 fluorescence as a spatial reference to quantify particles in the apical, basal, or total region of a Z-stack.

Both macros include a built-in graphical dialog that allows users to select their image folder and adjust analysis settings without editing the code. This makes the workflows accessible to researchers who use ImageJ but have little or no programming experience.

## User-Friendly Interface

When a macro is run, ImageJ displays a dialog box containing the available settings. Users can adjust parameters such as:

* Image directory
* File type
* Intensity limits
* Minimum and maximum particle size
* ZO-1 channel
* Apical, basal, or total analysis

After the settings are selected, the macro automatically processes all compatible images in the chosen folder.

No coding experience is required for routine use. Users only need to download the appropriate macro, open it in ImageJ or Fiji, select their settings through the dialog box, and run the analysis.

## Included Macros

### Standard POS Particle Counter

The standard particle-counting macro processes `.nd2` microscopy files and analyzes particles across the complete maximum-intensity projection.

For each compatible image, the macro:

* Creates a maximum-intensity Z-projection
* Isolates channel 2 as the particle channel
* Enhances image contrast
* Applies user-defined intensity limits
* Converts the processed image into a binary mask
* Quantifies particles within a user-defined size range
* Saves the processed image as a TIFF file
* Generates an ImageJ particle-analysis summary and overlay

This version is intended for experiments in which particles should be quantified across the entire projected image without separating the Z-stack into spatial regions.

### Apical and Basal POS Particle Counter

The apical/basal macro uses a user-selected ZO-1 channel to identify a spatial boundary within each microscopy Z-stack.

The macro calculates the mean ZO-1 fluorescence in each Z-slice and identifies the slice with the highest mean intensity. This reference slice is then used to define the image regions:

* **Apical:** Slices before the brightest ZO-1 slice
* **Basal:** Slices after the brightest ZO-1 slice
* **Total:** The complete particle-channel stack

The selected region is isolated from channel 2, converted into a maximum-intensity projection, thresholded using the Otsu method, and analyzed according to the selected particle-size range.

This version supports `.nd2` and `.lsm` files and is intended for experiments in which particle localization relative to a ZO-1-defined epithelial boundary is important.

## Features

* User-friendly graphical parameter selection
* No coding required for routine use
* Batch processing of microscopy image folders
* Adjustable intensity limits
* Adjustable particle-size limits
* Maximum-intensity Z-projection
* Automated binary-mask generation
* Particle counting using ImageJ’s **Analyze Particles** function
* Automatic creation of an output folder
* Saved TIFF masks for review and quality control
* Particle-analysis summaries and overlays
* Apical, basal, and total-region analysis
* Support for `.nd2` and `.lsm` microscopy formats

## Requirements

* Fiji or ImageJ
* Bio-Formats support for opening `.nd2` and `.lsm` files
* Multichannel microscopy Z-stacks
* Channel 2 containing the fluorescent particles to be analyzed
* A ZO-1 channel for the apical/basal workflow

Fiji is recommended because Bio-Formats is normally included with the installation.

## Downloading and Running a Macro

1. Download the desired macro file from this GitHub repository.
2. Open Fiji or ImageJ.
3. Open the macro through **Plugins → Macros → Run** and select the downloaded macro file.
4. Alternatively, open the ImageJ Script Editor through **File → New → Script**.
5. In the Script Editor, select **IJ1 Macro** as the scripting language and open or paste the macro code.
6. Click **Run**.
7. Use the displayed dialog box to select the image folder and analysis parameters.
8. Confirm the settings to begin batch processing.

The macro will process compatible images in the selected directory and create an output folder for the processed TIFF files.

## Standard POS Particle Counter Settings

The standard macro dialog includes:

* **Select a directory:** Folder containing the `.nd2` images
* **Max Percentage:** Upper display-intensity percentage
* **Min Percentage:** Lower display-intensity percentage
* **Micron Size Min:** Minimum accepted particle size
* **Micron Size Max:** Maximum accepted particle size

This version currently assumes that the source images have a 12-bit maximum intensity value of `4095`.

The selected percentages are converted into intensity values using:

```text
Minimum intensity = Minimum percentage × 4095
Maximum intensity = Maximum percentage × 4095
```

Images with a different bit depth or intensity range may require modification of the `MaxPixelValue` variable in the code.

## Apical and Basal POS Particle Counter Settings

The apical/basal macro dialog includes:

* **Select a directory:** Folder containing the microscopy images
* **Max Percentage:** Upper display-intensity percentage
* **Min Percentage:** Lower display-intensity percentage
* **Micron Size Min:** Minimum accepted particle size
* **Micron Size Max:** Maximum accepted particle size
* **ZO-1 Channel:** Channel used to identify the apical/basal boundary
* **Selection:** Apical, basal, or total analysis
* **File Type:** `.nd2` or `.lsm`

The particle signal is currently expected to be located in channel 2.

## Output

The macros automatically create an output folder inside the selected image directory.

The standard macro creates:

```text
finalfolder/
```

The apical/basal macro creates:

```text
1finalfolder/
```

Depending on the selected workflow, the output may include:

* Processed binary-mask TIFF files
* Apical, basal, or total filename prefixes
* Particle-count summaries
* Particle-measurement results
* Particle overlays

Example regional output filenames may resemble:

```text
apical_sample01.nd2.tif
basal_sample01.nd2.tif
total_sample01.nd2.tif
```

## Important Assumptions

Before running the macros, confirm that:

* Channel 2 contains the particle signal
* The selected ZO-1 channel is correct
* Particle-size values use the image’s calibrated spatial units
* Input images have a consistent channel arrangement
* Input images have a consistent Z-stack organization
* The selected threshold settings are appropriate for the dataset
* The generated masks accurately represent the particles of interest

The standard macro assumes a maximum pixel value of `4095`. The apical/basal macro determines its maximum pixel value from the processed image.

## Quality Control

Automated image analysis should be visually reviewed before the measurements are used for downstream analysis.

Recommended quality-control steps include:

* Comparing the binary masks with the original fluorescence images
* Confirming that true particles are retained
* Confirming that background signal is excluded
* Checking that particles are not incorrectly merged or fragmented
* Verifying that the detected ZO-1 slice represents the intended boundary
* Testing the settings on representative images before processing a complete dataset
* Applying consistent settings across experimental groups

Depending on the orientation and display settings of the source images, the generated binary mask may be inverted. Users should inspect the first processed images before analyzing the complete dataset.

## Limitations

* The macros assume a consistent channel arrangement across all images.
* The particle channel is currently fixed to channel 2.
* The standard macro only processes `.nd2` files.
* The standard macro assumes 12-bit images with a maximum intensity of `4095`.
* The brightest mean ZO-1 slice is treated as the apical/basal boundary and may not represent the correct biological boundary in every image.
* Images without sufficient slices above or below the detected boundary may not be suitable for regional analysis.
* Threshold and particle-size settings may require optimization for different microscopes, fluorophores, magnifications, or experimental conditions.
* Results depend on the spatial calibration stored in the source image.

## Intended Use

These macros were developed to reduce repetitive ImageJ processing and standardize fluorescent particle quantification across microscopy datasets.

The graphical dialog allows researchers to perform routine batch analysis without directly modifying the macro code, making the workflows easier to use and share with researchers who do not have a programming background.

The macros are intended as research tools and should be validated for each imaging setup and experimental application before being used for formal quantitative analysis.

## License

idk

## Author

Developed by Steven Xie.

Contributions, issue reports, and suggestions for improvements are welcome.
