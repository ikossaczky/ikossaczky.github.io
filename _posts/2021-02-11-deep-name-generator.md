---
layout: post
title: "Deep Name Generator"
date: 2021-02-11 10:00:00 +0100
categories: app
---
<!-- Loading tensorflow.js and jquery -->
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@2.0.1/dist/tf.min.js"></script>
<script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
<!-- Web interface -->
<form>
  <table>
    <tr>
      <td align="right">
        <label for="model">Language model:</label>
      </td>
      <td align="left">
        <select name="model" id="model"></select>
      </td>
    </tr>
    <tr>
      <td align="right">
        <label for="word_start">Word start:</label>
      </td>
      <td align="left">
        <input type="text" id="word_start" name="word_start" minlength="0" maxlength="20" size="12">
      </td>
    </tr>
    <tr>
      <td align="right">
        <label for="randomness">Randomness:</label>
      </td>
      <td align="left">
        <input type="number" id="randomness" name="randomness" min="0" max="10" value="0.8" size="3" step="0.1">
      </td>
    </tr>
    <tr>
      <td align="right">
      </td>
      <td align="left">
        <input type="button" value="Generate!" id="generate" onclick=predict>
      </td>
    </tr>
    <tr>
      <td colspan="2" align="center">
        <h3 id="output">&nbsp;</h3>
      </td>
    </tr>
  </table>
</form>


<p>
  <img src="" id="network_picture" width="35%" align="left" hspace="40" />
</p>
Deep name generator is a recurrent neural network for generating realistically sounding (and sometimes even existing) names, with interface written in javascript and running in browser.  

*Figure left: Architecture of the currently used model with input and output dimensions*
<h3 id="output">Parameters</h3>

- Language model: choose the language model. Different models were trained on different datasets and also with slightly different network architectures.

- Word start: letters that you want your word to begin with.

- Randomness: coefficient determining how random the output will be: higher value means more randomness. For `randomness=0`, the output is purely deterministic, randomness around 1 corresponds to standard probabilities learned by the model, and for high coefficients the outputs degenerate into random letter sequences.

<h3 id="output">Algorithm</h3>
The model generates the word sequentially: one character at each step, while always taking into account all previously generated characters. At each step, the already generated characters are encoded into sparse vectors having 1 at the *position of the character in alphabet* and 0 at all other positions (one-hot encoding). These vectors are fed into a neural network which outputs a vector $$P$$ with elements corresponding to the *character scores*: the score $$P_i$$ at position $$i$$ can be interpreted as *modelled probability* that letter at the $$i$$-th position of the alphabet is the next letter of the word. Using the randomness coefficient r, this vector is further processed using following formula for its elements:

$$\tilde P_i = \frac{P_i^{1/r}}{|| \sum_i^N P_i^{1/r} ||_1}$$

These new probabilities $$\tilde P_i$$ are then used for sampling the next letter. Note that for $$r \rightarrow 0$$ sampling degenerates into argmax and the output is deterministic. On the other hand, for $$r \rightarrow \infty$$, each letter is sampled with the same probability, which results in random letter sequences without any human language resemblance.

The neural network consists of one-dimensional convolutional layers with kernelsize 1, which are used to transform the sparse one-hot vector representation of a character into dense character embeding and to further process it. These are followed by one or more recurrent layers which integrate the informations from previous characters. The final layer uses softmax activation and outputs the score vector $$P$$.

<h4 id="output">Training procedure</h4>
The prediction process described above already indicates the training procedure: the model was simply trained to predict the letters of incomplete words -for the sake of this, only a simple dataset consisting of words is needed.

<h3 id="output">Implementation</h3>
The model was implemented and trained in Python [TensorFlow](https://www.tensorflow.org/) with [Keras API](https://keras.io/api/) and exported into the [TensorFlow.js](https://www.tensorflow.org/js). The inference is done in purely in javascript. This means, the neural network is currently running right in your browser. Source code can be found in this [repo](https://github.com/ikossaczky/deep-name-generator).

<h3 id="output">Data sources</h3>
<ul>
  <li>english-names: <a
      href="https://github.com/smashew/NameDatabases/blob/master/NamesDatabases/first%20names/us.txt">github.com/smashew/NameDatabases</a>
  </li>
  <li>english-places: <a
      href="https://raw.githubusercontent.com/bwghughes/badbatch/master/data/uk-towns-list/uk-towns.csv">github.com/bwghughes/badbatch</a>
  </li>
  <li>german-names: <a href="https://www.vornamen-weltweit.de/maennlich-deutsch.php"
      rel="nofollow">www.vornamen-weltweit.de/maennlich-deutsch</a>, <a
      href="https://www.vornamen-weltweit.de/weiblich-deutsch.php"
      rel="nofollow">www.vornamen-weltweit.de/weiblich-deutsch</a></li>
  <li>german-places: <a href="https://www.datenb%C3%B6rse.net/item/Liste_von_deutschen_Staedtenamen_.csv"
      rel="nofollow">www.datenbörse.net</a></li>
  <li>slovak-names: <a href="https://mix.sk/deti/babatko/mena-deti/" rel="nofollow">mix.sk</a></li>
  <li>slovak-places: <a href="https://www.e-obce.sk/zoznam_vsetkych_obci.html" rel="nofollow">www.e-obce.sk</a></li>
</ul>


<!-- Loading js interface to neural network directly from github using combinatronics.com-->
<script src="https://combinatronics.com/ikossaczky/deep-name-generator/master/network_interface.js"></script>