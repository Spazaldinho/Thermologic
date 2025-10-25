# ThermoLogic: AI-Powered Energy & Efficiency Dashboard

**ThermoLogic** is a comprehensive solution that combines machine learning for energy demand forecasting with real-time thermodynamic analysis for industrial efficiency optimization. The system reads live sensor data from machinery, calculates its operational efficiency, provides feedback for optimization, and visualizes both predicted energy needs and efficiency gains in an intuitive desktop application.

---

## ✨ Features

* **Energy Demand Forecasting:** Utilizes a Gradient Boosting Regressor model to predict energy requirements based on weather conditions like solar intensity and wind.
* **Real-Time Efficiency Monitoring:** Connects to an Arduino to process live temperature and pressure data from industrial equipment.
* **Thermodynamic Analysis:** Uses the `cantera` library to calculate thermodynamic properties like enthalpy ($h$) and determine the real-time isentropic efficiency ($\eta$) of machinery.
* **Automated Optimization Loop:** Implements a feedback mechanism that suggests adjustments to operating parameters to restore efficiency if it drops below a target threshold.
* **Interactive GUI Dashboard:** A user-friendly interface built with PyQt5 to visualize data, compare predicted vs. actual energy usage, and track efficiency improvements over time.
* **AI-Powered Insights:** Integrates with the Groq API to provide natural language summaries and key insights from the analyzed data.
* **Simple Data Input:** Features drag-and-drop functionality for loading CSV datasets for prediction.

---

## 🔧 How It Works

The project is divided into three core components that work together:

### 1. Energy Prediction Model

The script `rConversion.py` trains a `GradientBoostingRegressor` model on a dataset containing weather factors and corresponding energy demand. The features used for training are "Value", "Sunny_Or_Cloudy", and "Windy" to predict "Required_Energy". The trained model is then serialized and saved as `OntarioModel.pkl` for use in the main application.

### 2. Real-Time Efficiency Monitor

The scripts `app.py` and `app2.py` establish a serial connection to an Arduino device to receive a stream of sensor data (inlet/outlet pressure and temperature). Using the Cantera library, it calculates the isentropic efficiency with the formula:
$$\eta = \frac{h_1 - h_2}{h_1 - h_{2s}}$$
where $h_1$ is the inlet enthalpy, $h_2$ is the actual outlet enthalpy, and $h_{2s}$ is the ideal (isentropic) outlet enthalpy. The script `app2.py` continuously logs the actual efficiency vs. the optimized efficiency to `factorydata.csv`, creating a historical record of performance improvements.

### 3. GUI Dashboard

`pyqtgui.py` launches the main application window using PyQt5. It loads the pre-trained `OntarioModel.pkl` to make predictions on user-provided data.
* **Top Graph:** When a user drags and drops a CSV file, the application plots the model's predicted energy demand against the actual values from the file.
* **Bottom Graph:** The application reads `factorydata.csv` in real-time and plots the machine's original efficiency versus the improved efficiency achieved through the optimization feedback loop.
* **AI Summary Box:** The prediction results are sent to the Groq API, which returns a concise, human-readable analysis of the data.

---

## 🚀 Getting Started

### Prerequisites

* Python 3.8+
* An Arduino device configured to send comma-separated sensor data (`p1,p2,t1,t2`) over a serial port.
* A Groq API Key.

### Installation

1.  **Clone the repository:**
    ```bash
    git clone <your-repository-url>
    cd <your-repository-directory>
    ```

2.  **Install the required Python libraries:**
    ```bash
    pip install pandas scikit-learn joblib pyserial cantera pyqt5 matplotlib groq
    ```

3.  **Set up your environment variables:**
    The application `pyqtgui.py` expects the Groq API key to be set as an environment variable named `GROQ_API_KEY`.

4.  **Update Serial Port:**
    In `app.py` and `app2.py`, change the `arduino_port` variable from `"COM3"` to the correct serial port for your Arduino.

### Usage

1.  **Start the Data Collector:**
    Run `app2.py` to begin listening to the Arduino and logging efficiency data to `factorydata.csv`.
    ```bash
    python app2.py
    ```

2.  **Launch the Dashboard:**
    In a separate terminal, run the PyQt GUI application.
    ```bash
    python pyqtgui.py
    ```

3.  **Analyze Data:**
    * Drag and drop a CSV file with weather and energy data onto the designated area in the GUI.
    * Click the **"Process CSV"** button.
    * Observe the generated plots for energy prediction and efficiency improvement, and read the AI-generated summary.
