<p>
    The following is based on the <a href="http://www.intel.com/content/www/us/en/support/boards-and-kits/000005801.html" target="_blank">instructions from Intel</a>.
</p>

<ol>
    <li>Install Homebrew if it is not already installed:
        <pre>
        ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
        </pre>
    </li>
    <li>Install dfu-util, coreutils, and gnu-getopt if not already installed:
        <pre>
        brew install dfu-util coreutils gnu-getopt
        </pre>
    </li>
    <li>Download and extract the <a target="_blank" href="https://software.intel.com/en-us/iot/hardware/edison/downloads">Release #.# Yocto complete image</a> and Extract the contents of to your development directory (e.g. ~/Development/).</li>
    <li>Using Terminal (Command + Space and type Terminal), change to the directory where you extracted the Yocto image (your directory name and the name/version of the Edison folder may be different).
        <pre>
        cd ~/Development/iot-devkit-prof-dev-image-edison-20160315
        </pre>
    </li>
    <li>Ensure the Edison is disconnected from the USB ports on your computer.</li>
    <li>Run the flashall script and then plug the USB cables into board.
        <pre>
        ./flashall.sh
        </pre>
    </li>
</ol>
    
<blockquote>Note: The script can take up to 5 minutes to complete the flashing and another 2 minutes to reboot.</blockquote>

You should now have the latest image on your Intel® Edison development board.