# What-If Analysis of ML models for Grafana

![what-if-provisional](https://github.com/ertis-research/whatif-panel-for-Grafana/assets/48439828/852ab329-8012-4ad8-9c3d-1bf7bfbc8f46)

<div align="center">
  
![Grafana 8.5.3](https://img.shields.io/badge/Grafana-8.0.0-orange)
![GitHub watchers](https://img.shields.io/github/watchers/ertis-research/whatif-panel-for-Grafana)
![GitHub Repo stars](https://img.shields.io/github/stars/ertis-research/whatif-panel-for-Grafana)

</div>

This Grafana panel is a powerful tool for conducting **What-If predictive analysis with Artificial Intelligence models**, with a special focus on **Machine Learning**. Currently, the panel is compatible with **AI/ML models that receive a list of numeric fields through an HTTP request**.

The tool enables easy loading of data into the model directly from a data source configured in Grafana. It allows direct modifications to the data or through value intervals and presents the results in a pleasant and easily interpretable manner, using interactive graphs when necessary. Additionally, it provides the capability to export and import both the data and the obtained results.

Also we have made every effort to design the panel to be abstract and intuitive, while ensuring it is responsive to panel size and consistent with both dark and light modes. We hope you like it!

## :sparkles: Table of Contents

- [Installation](#wrench-installation)
  - [Requirements](#requirements)
  - [Development mode](#development-mode)
  - [Production mode](#production-mode)
- [Getting started](#rocket-getting-started)
- [Documentation](#page_facing_up-documentation)

## :wrench: Installation

### Requirements

- [Node.js](https://nodejs.org/es) - version 16 or above (v18.13.0 has been used for development)
- [Yarn](https://yarnpkg.com/) - version 1 (v1.22.11 has been used for development)
- [Grafana](https://grafana.com/) - version 8 (v8.5.3 has been used for development, but higher versions will be tested soon)

To compile and install the plugin, it is necessary to download the code from this repository. This can be achieved by cloning it using the following command:

```bash
git clone https://github.com/ertis-research/whatif-panel-for-Grafana.git
cd whatif-panel-for-Grafana
```
### Development mode

To add the plugin in a development environment, you need to include the folder containing the complete code inside the [Grafana designated folder for plugins](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#plugins). Then, the following commands must be executed to install dependencies and build plugin:

```bash
yarn install
yarn dev
```

This will enable the plugin in the instance of Grafana where it has been placed, remaining in watch mode waiting for changes to be saved.

> **Warning**
> If the plugin is not available, it is possible that the Grafana instance is not configured correctly for development. This can be verified by checking the *grafana.ini* file and checking that the [*app_mode*](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#app_mode) option is set to *development*.

### Production mode

To allow the plugin to run, it needs to be signed following the [guidelines](https://grafana.com/docs/grafana/latest/developers/plugins/sign-a-plugin/) provided by Grafana. However, there is also the option to explicitly indicate that the plugin can be executed without signature. To do this, its identifier must be included in the [*allow_loading_unsigned_plugins*](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#allow_loading_unsigned_plugins) option of the *grafana.ini* file.

To build the plugin for a production environment, run the following command to install dependencies and build plugin:

```bash
yarn install
yarn build
```

As output, a folder called *dist* will be generated in the root of the project, which will contain the plugin build. **This folder should be placed inside the [plugins folder](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#plugins) of the production environment** (it is recommended to rename it to the plugin identifier). 

Once these steps are completed, the plugin will be available for selection when adding a new panel to a dashboard.


## :rocket: Getting started

> **Note**
> This explanation is simplified for common users and assumes that the plugin has already been added and correctly [configured](#configuration) with the necessary AI/ML models. For more detailed information, it is recommended to consult the rest of the [documentation](#page_facing_up-documentation) provided.

The functionality of the plugin is divided into 5 steps:

#### Step 1: Select model

To begin, the AI/ML model to be used to perform the analysis must be selected. Once chosen, the tags associated with that model will be displayed in step 3. **Although the tool can apply multiple data collections, all of them will use the same model to make predictions**.

#### Step 2: Import data

Several options are provided for entering model tag data. You can select a **specific date and time**, **upload a CSV file**, or **use a variable from the Grafana dashboard** (in particular, [*from* and *to*](https://grafana.com/docs/grafana/latest/dashboards/variables/add-template-variables/#__from-and-__to)). Adding data in any of these ways will create a new collection in the drop-down menu and display the imported values in their respective tags. It is possible to add multiple collections of data (no limit set), even if they correspond to the same datetime.

#### Step 3: Modify data

At this point, it is possible to modify the values of the tags whose variation is to be analysed. To accomplish this, there are 3 options available:

- **Modify values directly:** Each tag consists of two fields: the left one shows the value obtained when importing the data, while the right one is initially empty. If the right field is filled in, the prediction will consider this as the new value of the tag. We can modify as many tags as we want in this way and **the prediction will be done jointly**, i.e. assuming the change of all of them at the same time in the same prediction.

- **Use intervals:** When a [interval](#interval-definition) of values has been correctly indicated, the checkbox to the right of the second field of each tag will be enabled. By ticking this box, you indicate your wish to analyse that tag with respect to the set range. It is possible to analyse more than one tag for comparison purposes. During the prediction, the values within the range will be applied, **in isolation**, to each selected tag, keeping the original value of the rest of the selected tags.

- **Merging other two options:** If modifications are made directly to some values and a interval is applied to other tags, the prediction modified by the **direct values shall be considered as the basis for the application of the interval**. This means that the modified values of the tags will be taken into account instead of the original values during the analysis of each tag with respect to the indicated interval. On the other hand, if a new value is assigned to a tag and at the same time it is selected for analysis with respect to the interval, the new value will not be taken into account in no case.

In addition, a series of [filters](#tag-filtering) have been enabled to facilitate the search and modification of the desired tags.

#### Step 4: Predict result

In case you have configured extra information for the selected model, this will be displayed in a section within this step after the data import, as long as you have obtained some information from the assigned query.

Once you have modified the data you want to analyse, click on the *predict all* button. This will **run the predictions for all data collections simultaneously** and display the results just below.

Results may include:

- **Original value:** This value will appear as long as there is no missing data in the original data collection. It represents the prediction for the data collection without considering any modifications.
- **New value:** This value will appear when at least one tag has a directly modified value. It represents the prediction for the data collection in which the original values are replaced by new values, in the tags where they exist. If there is any tag marked to be analysed by an interval, the corresponding original value will be considered.
- **Comparative graph:** This [graph](#graph-information) will appear when the interval is active and there is at least one tag with the box ticked for interval analysis. Each line of the graph will represent the predicted values obtained by considering, individually for each tag, the values within the range instead of their original value.

In case you want to modify the data again, you need to click on the red *Modify data again* button located where the predict button used to be. This will discard all results and allow rewriting in any of the available data collections, keeping the modifications previously indicated.

#### Step 5: Export data (optional)

This step allows you to export the information contained in the panel in order to be able to [import it](#csv-importexport) later or to perform a more exhaustive analysis with other types of tools. This button will only export data from the current data collection and not from all existing data collections.

The export button will be available from step 3 onwards, where it will be possible to download a [CSV](#csv-scheme) with the interval configuration if active, the original and new value (if any) of each tag and whether or not the tag is marked for interval analysis. After executing step 4, the result for the different predictions will be added to this information.

## :page_facing_up: Documentation

### Configuration

#### Basic options

In the general tab, you will find the options that are common to the whole panel. Specifically, they are the following:

- **Plugin language:** It is possible to change the language of the elements that make up the panel to English or Spanish. The default value is English. This will not affect the configuration section.
- **Decimals:** Defines the number of decimals to which the prediction results will be rounded. This rounding will not be applied for the prediction or export, but will only be visual when displaying the results both individually and in the graph.

#### Models

#### Formats

#### Data import queries

#### Extra information

### Panel usage

#### Tag filtering

#### Interval definition

#### Graph information

#### CSV import/export

##### CSV scheme



