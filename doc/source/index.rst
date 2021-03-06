.. CSTR NN-TTS documentation master file, created by
   sphinx-quickstart on Mon Feb 15 11:11:14 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to CSTR's NN-TTS documentation!
=======================================

Contents:

.. toctree::
   :maxdepth: 2



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

Get Started
=========================

Required softwares/tools
------------------------

The system has been tested in linux environment, and the following packages are required.
 * Python 2.6/2.7
 * Theano 0.6/0.7 (http://deeplearning.net/software/theano/). 0.8 version is not tested yet.
 * Bandmat 0.5: https://pypi.python.org/pypi/bandmat/0.5
 * SPTK: http://sp-tk.sourceforge.net/


Data Preparation for a neural network (NN) based speech synthesis system
------------------------------------------------------------------------

To build a NN system, you need to prepare linguistic features as system input and acoustic features as system output. Please follow the instructions in this section to prepare your data.

Input Linguistic Features
+++++++++++++++++++++++++

Neural networks take vectors as input, so the alphabet representation of linguistic features needs to be vectorized.

1. **HTS style**: Please check the HTS demo for the HTS style labels (http://hts.sp.nitech.ac.jp/).
    * Provide HTS full-context labels with state-level alignments.
    * Provide a question file that matches the HTS labels. 
    * The questions in the question file will be used to convert the full-context labels into binary and/or numerical features for vectorization. It is suggested to do a manual selection of the questions, as the number of questions will affect the dimensionality of the vectorized input features.
    * Different from the HTS format question, the NN system also supports to extract numerical values using '**CQS**', e.g., ** CQS "Pos_C-Word_in_C-Phrase(Fw)" {:(\d+)+}**, where '**:**' and '**+**' are separators, and '**(\d+)**' is a regular expression to match a numerical feature.

2. **"Composed" style**:

3. **Direct *vectorized* input**: If you prefer to do vectorization yourself, you can feed the system binary files directly. Please prepare your binary files with the following instructions:
    * Align the input feature vectors with the acoustic features. Input and output features should have the same number of frames.
    * Store the data in binary format with '**float32**' precision.
    * In the config file, use an empty question file, and set *appended_input_dim* to be the dimensionality of the input vector.
    * Note: voice conversion can use this kind of direct vectorized input. 


Output Acoustic Features
++++++++++++++++++++++++

The default setting is assuming you use the STRAIGHT vocoder (c version). This vocoder is free for academic users. The output includes 
    * mel-cepstral coeffcients (MCC),
    * band aperiodicities (BAP),
    * Fundamental frequency (F0) in logarithmic scale. 
Please provide the three features in binary format with 'float32' precision, in the config file, provide the dimensionality of each feature, for example
    * [Outputs] mgc  :  60
    * [Outputs] dmgc :  180
**dmgc** means the dimensionality of MCC with delta and delta delta features. If **dmgc** is set to 60, only the static features are used.
Please also tell the file extension for each feature, for example
    * [Extensions] mgc_ext : .mgc
    * [Extensions] bap_ext : .bap
    * [Extensions] lf0_ext : .lf0
   
The open-source WORLD vocoder is also supported. The modified version for SPSS can be found in the repository.
    
If you have your preferred vocoder, please try to give a nick name to each feature to match the supported ones.

Recipes
---------------

In the system, several recipes for standard neural network architectures are provided. They are described below:

Architecture
+++++++++++++++

The system supports a flexible way to change neural network architectures by changing the config file in the [Architecture] section:
    * hidden_layer_size  : [512, 512, 512, 512]
    * hidden_layer_type  : ['TANH', 'TANH', 'TANH', 'TANH']
By default, feedforward neural network is used. But the system supports various types of hidden layers:
    * **'TANH'** : The Hyperbolic Tangent activation function
    * **'RNN'**  : The simple but standard recurrent neural network unit
    * **'LSTM'** : The standard Long Short-Term Memory unit
    * **'GRU'**  : The gated recurrent unit
    * **'SLSTM'**: The simplified LSTM unit
    * **'BLSTM'**: The bidirectional LSTM unit
You can define your own architecture by choosing a hidden unit at each hidden layer. For each type of hidden layer, please check the **Models** section.

Deep Feedforward Neural Network
+++++++++++++++++++++++++++++++

An example config file can be found in the './recipes/dnn' directory. Please use 'submit.sh ./run_lstm.py ./recipes/dnn/feed_foward_dnn.conf' to build the feedforward neural network. Please modify the config file to adapt to your own working environment (e.g., data path).

Mixture Density Neural Network
++++++++++++++++++++++++++++++

(Deep) Long Short-Term Memory (LSTM) based Recurrent Neural Network (RNN)
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

An example config file is provided in './recipes/dnn/hybrid_lstm.conf'. Follow the same recipe as that in the deep feedforward neural network section.

(Deep/Hybrid) Bidirectional LSTM-based RNN
++++++++++++++++++++++++++++++++++++++++++

Example config files are provided in './recipes/blstm' directory. 'blstm.conf' is for multiple bidrectional LSTM layers, while 'hybrid_blstm.conf' is for a hybrid architecture, that uses several feedforward layers at the bottom, and one BLSTM layer at the top.

Variants of LSTM
----------------

This recipe is to support the paper by Wu & King (ICASSP 2016). Several variants of LSTMs are provided. Please use the corresponding config files to do the experiments.


Stacked Bottlenecks
+++++++++++++++++++



Trajectory modelling
++++++++++++++++++++

Models
=========================

Deep Feedforward/Recurrent Networks
-----------------------------------

This is something I want to say that is not in the docstring.


.. autoclass:: models.deep_rnn.DeepRecurrentNetwork
   :members:  __init__, build_finetune_functions, parameter_prediction
   
   
Layers
=========================

Recurrent Neural Network units
------------------------------

This is something I want to say that is not in the docstring.

.. autoclass:: layers.gating.VanillaRNN
   :members:  __init__, recurrent_as_activation_function
   
.. autoclass:: layers.gating.LstmBase
   :members:  __init__, recurrent_fn, lstm_as_activation_function
   
.. autoclass:: layers.gating.VanillaLstm
   :members:  __init__, lstm_as_activation_function   

.. autoclass:: layers.gating.LstmNFG
   :members:  __init__, lstm_as_activation_function   
   
.. autoclass:: layers.gating.LstmNIG
   :members:  __init__, lstm_as_activation_function   
   
.. autoclass:: layers.gating.LstmNOG
   :members:  __init__, lstm_as_activation_function   
   
.. autoclass:: layers.gating.LstmNoPeepholes
   :members:  __init__, lstm_as_activation_function   
   
.. autoclass:: layers.gating.SimplifiedLstm
   :members:  __init__, lstm_as_activation_function   
   
.. autoclass:: layers.gating.GatedRecurrentUnit
   :members:  __init__, lstm_as_activation_function   
   
                     
I/O functions
=========================

Binary I/O collections
----------------------

.. autoclass:: io_funcs.binary_io.BinaryIOCollection
   :members:  load_binary_file, array_to_binary_file, load_binary_file_frame
   
   
Utils
=========================

Data Provider
----------------

.. autoclass:: utils.providers.ListDataProvider
   :members:  __init__, reset, make_shared, load_next_utterance, load_next_partition

Front-end
========================

Label normalisation
-------------------

.. autoclass:: frontend.label_normalisation.HTSLabelNormalisation
    :members: __init__, load_labels_with_state_alignment, pattern_matching, pattern_matching_binary, pattern_matching_continous_position, load_question_set, load_question_set_continous, wildcards2regex
