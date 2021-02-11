---
layout: post
title: "Deep Name Generator"
date: 2021-02-11 00:00:00 +0100
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

<!-- <h3>Network architecture:</h3>
 <div style="float: left;">
   <img src="" id="network_picture" width="50%"/>
   asdasd
   sadasd
   sadadas
   sdsadas                     
 </div> -->
<p>
  <img src="" id="network_picture" width="35%" align="left" hspace="40" />
  <i> Figure left: Architecture of the currently used model with input and output dimensions </i>
</p>
<h3 id="output">Parameters</h3>

- Language model: choose the language model. Different models were trained on different datasets and also with slightly different network architectures.

- Word start: Letters that you want your word to begin with.

- Randomness: coefficient determining how random the output will be (higher means more randomness). At each inference step, the network predicts vector $$P$$ with elements corresponding to the letter scores. These scores can also be seen as a "probabilities" that the letter is the next letter in the word. Using the randomness coefficient r, this vector is further processed using following formula:

$$\tilde P = || \sum_i^N P_i^{1/r} ||_1$$

These new probabilities $$\tilde P$$ are then used for sampling the next letter. Note that for $$r \rightarrow 0$$ sampling degenerates into argmax and the output is deterministic. On the other hand, for $$r \rightarrow \infty$$, each letter is sampled with the same probability, which results in random letter sequences without any human language resemblance.

<h3 id="output">Implementation details</h3>
The model was implemented and trained in Python TensorFlow with Keras API and exported into the TensorFlow.js. The
inference is done in purely in javascript. This means, the neural network is currently running right in your browser.

<h3 id="output">Data sources</h3>
<ul>
  <li>english-names: <a
      href="https://github.com/smashew/NameDatabases/blob/master/NamesDatabases/first%20names/us.txt">github.com/smashew/NameDatabases</a>
  </li>
  <li>english-places: <a
      href="https://github.com/bwghughes/badbatch/blob/master/data/uk-towns-list/uk-towns.csvtop-1000-names-in-england-and-wales-2015.html">github.com/bwghughes/badbatch</a>
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