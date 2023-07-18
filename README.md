Universal Event Detection in Time Series
==========================================================

Welcome to **Event Detector**, a Python package for detecting events in time series data. The emphasis of this package
is on offering useful machine learning functionalities, such as customizing and fitting the model on multidimensional
time series, training on large datasets, ensemble models, and providing rich support for event detection in time
series.

To get started using **Event Detector**, simply follow the instructions below to install the required packages and
dependencies.

1. Clone the repository:

<pre><code>git clone https://github.com/menouarazib/eventdetector.git
cd eventdetector
</code></pre>

2. Create a virtual environment:

<pre><code>python -m venv env
source env/bin/activate  # for Linux/MacOS
env\Scripts\activate.bat  # for partitions
</code></pre>

3. Install the required packages:

<pre><code>pip install -r requirements.txt</code></pre>

4. Quickstart Example

To quickly get started with the Event Detection in Time Series package, follow the steps below:

- You can either download the datasets and event catalogs manually or use the built-in methods for the desired application:
  - Martian Bow Shock: `eventdetector.load_martian_bow_shock()`
      - [bow_shock_dataset](https://onedrive.live.com/download?resid=4258578D3874B519%21151&authkey=!AGic2wBEAfnuBYg)
      - [bow_shock_events](https://onedrive.live.com/download?resid=4258578D3874B519%21150&authkey=!ACMf4jI2uwvVoec)
  - Credit Card Fraud: `eventdetector.load_credit_card_fraud()`
      - [credit_card_dataset](https://onedrive.live.com/download?resid=4258578D3874B519%21129&authkey=!ANgPyE0DphR9gTM)
      - [credit_card_events](https://onedrive.live.com/download?resid=4258578D3874B519%21147&authkey=!AIANbQqstMFG_zs)
- Code Implementations:
  - Credit Card Fraud:
    ```python
    from eventdetector import load_credit_card_fraud
    from eventdetector.metamodel.meta_model import MetaModel
    
    dataset, events = load_credit_card_fraud()
    meta_model = MetaModel(dataset=dataset, events=events, width=3, step=1, output_dir='fraud', batch_size=3000)
    # Prepare the events and dataset for computing op.
    meta_model.prepare_data_and_computing_op()
    # Builds a stacking learning pipeline using the provided models and hyperparameters.
    meta_model.build_stacking_learning()
    # Run the Event Extraction Optimization process.
    meta_model.event_extraction_optimization()
    # Plot the results: Losses, true/predicted op, true/predicted events, deltat_t.
    meta_model.plot_save(show_plots=True)
    ```
  - Martian Bow Shock:
    ```python
    from eventdetector import load_martian_bow_shock
    from eventdetector.metamodel.meta_model import MetaModel
    
    dataset, events = load_martian_bow_shock()
    
    # Create the MetaModel.
    meta_model = MetaModel(output_dir="martian_bow_shocks", dataset=dataset, events=events, width=45, step=1,
                           time_window=5400, batch_size=3000)
    # Prepare the events and dataset for computing op.
    meta_model.prepare_data_and_computing_op()
    # Builds a stacking learning pipeline using the provided models and hyperparameters.
    meta_model.build_stacking_learning()
    # Run the Event Extraction Optimization process.
    meta_model.event_extraction_optimization()
    # Plot the results: Losses, true/predicted op, true/predicted events, deltat_t.
    meta_model.plot_save(show_plots=True)
    ```
    
5. MetaModel Arguments:

Argument | Description
   ---------------------------- | --------------------------------------------------------------
   output_dir | The name or path of the directory where all outputs will be saved. If `output_dir` is a folder name, it will create the full path.
   dataset | The input dataset as `pd.DataFrame`.
   events | The input events as a list or `pd.DataFrame`.
   width | The width to be used for creating overalapping partitions.
   step | The step size between two successive partitions.
   kwargs | Optional keyword arguments:
   t_max | The maximum total time related to sigma. Default: `(3 * w_s) / 2)`.
   delta | The maximum time tolerance used to determine the correspondence between a predicted event and its actual counterpart. Default: `w_s`.
   s_h | A step parameter for the peak height threshold `h`. Default: `0.05`.
   epsilon | A small constant used to control the size of the set containing top models with the lowest MSE values. Default: `0.0002`.
   pa | The patience for the early stopping algorithm. Default: `5`.
   t_r | The ratio threshold for the early stopping algorithm. Default: `0.97`.
   time_window | The 'time_window' parameter is crucial for controlling the amount of data used in the dataset. It should be specified as a number of units of time. By default, it is set to `None`, which means that all available data will be used. However, if a value is provided, the dataset will only include a specific interval of data around each reference event. This interval consists of data from both the left and right sides of each event, with a duration equal to the specified `time_window`. Setting a time_window can offer several advantages, including speeding up the training process and improving the neural networks' understanding for rare events.
   models | Determine the type of deep learning models and the number of instances to use. Default: `[(model, 3) for model in [FFN]]`.
   hyperparams_ffn | Specify for the FFN the maximum number of layers, the minimum and the maximum number of neurons per layer. Default: `(3, 64, 256)`.
   hyperparams_cnn | Specify for the CNN the minimum and maximum number of filters, the minimum, the maximum kernel size, and maximum number of pooling layers. Default: `(16, 64, 3, 8 , 2)`.
   hyperparams_rnn | Specify for the RNN the maximum number of RNN layers, the minimum and the maximum number of hidden units. Default: `(1, 16, 128)`.
   hyperparams_mm_network | Specify for the MetaModel network the number of layers and the number of neurons per layer. Default: `(1, 32)`.
   epochs | The number of epochs to train different models. Default: `256`.
   batch_size | The number of samples per gradient update. Default: `32`.
   fill_nan | Specifies the method to use for filling `NaN` values in the dataset. Supported methods are 'zeros', 'ffill', 'bfill', and 'median'. Default: `"zeros"`.
   type_training | Specify the type of training technique to use for the MetaModel. Supported techniques are 'average' and 'ffn'. Default: `"average"`.
   scaler | The type of scaler to use for preprocessing the data. Possible values are "MinMaxScaler", "StandardScaler", and "RobustScaler". Default: `"StandardScaler"`.
   use_kfold | Whether to use k-fold cross-validation technique or not. Default: `False`.
   test_size | The proportion of the dataset to include in the test split. Should be between 0 and 1. Default is `0.2`.
   val_size |The proportion of the training set to use for validation. Should be a value between 0 and 1. Default is `0.2`.
   use_multiprocessing | Whether to use multiprocessing or not for the event exctraction optimization. The default value is `False`.
   save_models_as_dot_format | Whether to save the models as a dot format file. The default value is `False`. If set to True, then you should have `graphviz` software to be installed on your machine.
   
## How to credit our package

If you use our package, please cite the following paper:
```python
@misc{azib_renard_garnier_génot_andré_2023,
title={Universal Event Detection in Time Series},
url={osf.io/uabjg},
publisher={OSF Preprints},
author={Azib, Menouar and Renard, Benjamin and Garnier, Philippe and Génot, Vincent and André, Nicolas},
year={2023},
month={Jul}
}
```

