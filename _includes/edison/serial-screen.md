<div id="run-tabs">
  <ul>
    <li><a href="#windows"><span>Windows</span></a></li>
    <li><a href="#mac"><span>Mac OS X</span></a></li>
  </ul>
  <div id="windows">
    <h3>Installing Application Dependencies Using Windows (PuTTy)</h3>
    <ol>
      <li>In PuTTY, double-check that you are in the <b>Session</b> screen by looking at the left-hand tree menu.</li>
      <li>Select the <b>Serial</b> radio button under <i>Connection type</i>.</li>
      <li>Specify the destination you want to connect to:
        <ul>
          <li><b>Serial line</b>:<br/>
          Use the COM port number designated for your device. This should take the form of <b>COM#</b> where # is the number of your USB Serial Port (for example, COM3). See <a href="https://software.intel.com/en-us/node/628227" target="_blank">Intel's documentation</a> for more information.</li>
          <li><b>Speed</b>:<br/>
          Always use <b>115200</b> for the baud rate.</li>
        </ul>
      </li>
      <li>Click <b>Open</b> to open the serial connection.</li>
      <li>When you see a blank command prompt, press <kbd>Enter</kbd>. At the login prompt, login with the username <b>root</b> and the password (if you created one).</li>
    </ol>
  </div>
  <div id="mac">
    <h3>Installing Application Dependencies Using Mac (Screen)</h3>
    You can execute commands on the Edison using the <b>screen</b> utility.
    
    <ol>
      <li>Open <b>Terminal</b></li>
      <li>Type <code>screen /dev/cu.usbs</code> then press <kbd>Tab</kbd> to autocomplete.</li>
      <li>Add <code>115200 -L</code> after the device identifier, and press <kbd>Enter</kbd>.</li>
      <li>At the login prompt, login with the username <b>root</b> and the password (if you created one).</li>
    </ol>
  </div>
</div>

<script>
$( "#run-tabs" ).tabs();
</script>