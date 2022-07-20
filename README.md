# Opcodes | Definitions for private servers
Full list of opcodes / definitions for patch v92.4 and v100.2

## This is a data-type repo, not a working script itself
Feel free to contibute with your data! -> open pull request

### How to update any script manual monkaS
* Open index.js (edit it via any text editor VS code studio pref)
* Search for definitions: (example)

dispatch.hook('C_PLAYER_LOCATION', <b>6</b>, (event) => .....

* Check out [DEFINITIONS PATCH v100.2](https://github.com/ketchdrown/data-private-servers/tree/master/v100.02/definitions)
* Check if the value after definition inside the code equeals the one that is working for x game patch
* If it is not change the value (in this case it will equals "5"

dispatch.hook('C_PLAYER_LOCATION', <b>5</b>, (event) => .....

* Example no.2
* What if there is something more then a number?
 
dispatch.hook('S_PLAYER_STAT_UPDATE', <b>dispatch.majorPatchVersion >= 109 ? 16 : 15</b>, (event) => ....

* Delete "dispatch.majorPatchVersion >= 109 ? 16 : 15"
* Type pure number (in this case = 14 for patch 100.2)

dispatch.hook('S_PLAYER_STAT_UPDATE', <b>14</b>, (event) => ....

* Save the file -> disable autoupdate so your edits won't change
