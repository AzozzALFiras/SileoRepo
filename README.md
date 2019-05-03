# SileoRepo
Support Source for Sileo Store 

# Go to setings repo

### searching for Packages File Compression

```php

Make it {All}

```


### searching for Packages Validation

```php

Make it {MD5Sum & SHA1 & SHA256}

```


### Create a file named Sileo.php

```php

<?php

// Write here the path of the connection file in the database
require_once('system/');
base_url();


$rewrite_mod = get_option('rewrite_mod');
switch($rewrite_mod){
	case 3:
		$rewrite_url = array('sileoview' => 'sileoview/%d', 'sileoview_nohistory' => 'sileoview/%d/nohistory', 'screenshot' => 'screenshot/%d', 'history' => 'history/%d', 'contact' => 'contact/%d', 'section' => 'section/%d', 'report' => 'report/%d', 'report_support' => 'report/%1$d/%2$d', 'more' => 'more/%d', 'more_offset' => 'more/%1$d/%2$d', 'erorr' => 'erorr');
		break;
	case 1:
	case 2:
	default:
		$rewrite_url = array('sileoview' => 'sileo.php?method=sileoview&amp;pid=%d', 'view_nohistory' => 'sileo.php?pid=%d&amp;addr=nohistory', 'screenshot' => 'sileo.php?method=screenshot&amp;pid=%d', 'history' => 'sileo.php?method=history&amp;pid=%d', 'contact' => 'sileo.php?method=contact&amp;pid=%d', 'section' => 'sileo.php?method=section&amp;pid=%d', 'report' => 'sileo.php?method=report&amp;pid=%d', 'report_support' => 'sileo.php?method=report&amp;pid=%1$d&amp;support=%2$d', 'more' => 'sileo.php?method=more&amp;pid=%d', 'more_offset' => 'sileo.php?method=more&amp;pid=%1$d&amp;offset=%2$d', 'erorr' => 'erorr');
		break;
}
function echo_rewrite_url($type, $variable1, $variable2=''){
	global $rewrite_url;
	echo SITE_URL;
	printf($rewrite_url[$type], $variable1, $variable2);
}

class_loader('Mobile_Detect');
$detect = new Mobile_Detect;
if(!$detect->isiOS()){
	if (DCRM_PCINDEX == 2) {
		header("Location: ".SITE_URL.$rewrite_url['erorr']);
		exit();
	} else {
		$isCydia = false;
	}
} else {
	if (DCRM_MOBILE == 2) {
		if (!strpos($detect->getUserAgent(), 'Cydia')) {
			$isCydia = false;
		} else {
			$isCydia = true;
		}
	} else {
		exit('Sorry');
	}
}
if (file_exists('Release')) {
	$release = file('Release');
	$release_origin = __('No Name');
	$release_mtime = filemtime('Release');
	$release_time = date('Y-m-d H:i:s',$release_mtime);
	foreach ($release as $line) {
		if(preg_match('#^Origin#', $line)) {
			$release_origin = trim(preg_replace("#^(.+):\\s*(.+)#","$2", $line));
		}
		if(preg_match("#^Description#", $line)) {
			$release_description = trim(preg_replace("#^(.+):\\s*(.+)#","$2", $line));
		}
	}
} else {
	$release_origin = __('DCRM ');
}
if (isset($_GET['pid'])) {
	if (ctype_digit($_GET['pid']) && intval($_GET['pid']) <= 10000) {
		function device_check(){
			global $detect;
			$device_type = array('iPhone', 'iPod', 'iPad');
			for ($i = 0; $i < count($device_type); $i++) {
				$check = $detect->version($device_type[$i]);
				if ($check !== false) {
					if (isset($_SERVER['HTTP_X_MACHINE'])) {
						$DEVICE = $_SERVER['HTTP_X_MACHINE'];
					} else {
						$DEVICE = 'Unknown';
					}
					$OS = str_replace('_', '.', $check);
					break;
				}
			}
			return array('DEVICE' => $DEVICE, 'OS' => $OS);
		}

		if (isset($_GET['method']) && $_GET['method'] == 'screenshot') {
			$index = 2;
			$title = __('View Screenshots');
		} elseif (isset($_GET['method']) && $_GET['method'] == 'report') {
			$device_info = device_check();
			if (!isset($_GET['support'])) {
				$index = 3;
			} else {
				if ($_GET['support'] == '1') {
					$support = 1;
				} elseif ($_GET['support'] == '2') {
					$support = 2;
				} elseif ($_GET['support'] == '3') {
					$support = 3;
				} else {
					$support = 0;
				}
				$index = 4;
			}
			$title = __('Report Problems');
		} elseif (isset($_GET['method']) && $_GET['method'] == 'history') {
			$index = 5;
			$title = __('All Tweak Updates  ');
		} elseif (isset($_GET['method']) && $_GET['method'] == 'contact') {
			$index = 6;
			$title = __('Contact us');
		} elseif (isset($_GET['method']) && $_GET['method'] == 'section') {
			$index = 7;
			$title = __('Package Category');
		} elseif (isset($_GET['method']) && $_GET['method'] == 'more') {
			$index = 8;
			$section = DB::fetch_first("SELECT `Name`, `Icon` FROM `".DCRM_CON_PREFIX."Sections` WHERE `ID` = '".(int)$_GET['pid']."'");
			$q_name = DB::real_escape_string($section['Name']);
			if (isset($_GET['offset']) && !empty($_GET['offset']) && ctype_digit($_GET['offset'])) {
				$offset = intval($_GET['offset']);
			} else {
				$offset = 0;
			}
			$packages = DB::fetch_all("SELECT `ID`, `Name`, `Description`, `Version`, `DownloadTimes`, `Package` FROM `".DCRM_CON_PREFIX."Packages` WHERE (`Stat` = '1' AND `Section` = '".$q_name."') ORDER BY `ID` DESC LIMIT 10 OFFSET ".$offset);
			foreach($packages as $package){
				if(!empty($package)){
					if ($isCydia) { ?>
					
					
					
				
<?php }?>
           
    
     
      
        
<?php
				}
			}
			exit();
		} elseif (!isset($_GET['method']) || (isset($_GET['method']) && $_GET['method'] == 'view')){
			$index = 1;
			$title = ('');
			$package_id = (int)DB::real_escape_string($_GET['pid']);
			$package_info = DB::fetch_first("SELECT `Name`, `Version`, `Author`, `Package`, `Description`, `Source`, `DownloadTimes`, `Multi`, `CreateStamp`, `Size`, `Website`, `Installed-Size`, `Section`, `Homepage`, `Tag`, `Level`, `Price`, `Purchase_Link`, `Changelog`, `Changelog_Older_Shows`, `Video_Preview`, `System_Support`, `ScreenShots` FROM `".DCRM_CON_PREFIX."Packages` WHERE `ID` = '".$package_id."' LIMIT 1");
			if ($package_info) $title = $title.'  '.$package_info['Name'];
		} 
	elseif (!isset($_GET['method']) || (isset($_GET['method']) && $_GET['method'] == 'sileoview')){
			$index = 15;
			$title = ('');
			$package_id = (int)DB::real_escape_string($_GET['pid']);
			$package_info = DB::fetch_first("SELECT `Name`, `Version`, `Author`, `Package`, `Description`, `Source`, `DownloadTimes`, `Multi`, `CreateStamp`, `Size`, `Website`, `Installed-Size`, `Section`, `Homepage`, `Tag`, `Level`, `Price`, `Purchase_Link`, `Changelog`, `Changelog_Older_Shows`, `Video_Preview`, `System_Support`, `ScreenShots` FROM `".DCRM_CON_PREFIX."Packages` WHERE `ID` = '".$package_id."' LIMIT 1");
			if ($package_info) $title = $title.'  '.$package_info['Name'];
		} 
		
		
		else {
			httpinfo(405);
			exit();
		}
	} else {
		httpinfo(405);
		exit();
	}
} elseif (!isset($_GET['method'])) {
	$index = 0;
	$title = $release_origin;
} else {
	httpinfo(405);
	exit();
}
?>

<?php
$repo_url = base64_decode(DCRM_REPOURL);
if ($index == 0) {
	if (!$isCydia) {
?>


                            

<?php
	}
	
	
} elseif ($index == 15) {
	if (!$package_info) {
?>






<?php
			}
		}
		if(!defined('DCRM_DESCRIPTION')) define('DCRM_DESCRIPTION', 2);
		if (DCRM_MOREINFO == 2 || DCRM_DESCRIPTION == 2 || (empty($package_info['Multi']) && DCRM_MULTIINFO == 2)) {
?>

{
  "minVersion": "0.1",
  "headerImage": "https://pbs.twimg.com/profile_banners/3686220322/1553719342/1500x500",
  "tabs": [
    {
      "tabname": "Details",
      "views": [
        {
          "title": "<?php echo($package_info['Name']); ?>",
          "useBoldText": true,
          "useBottomMargin": false,
          "class": "DepictionSubheaderView"
        },
        {
          "title": "<?php echo($package_info['Description']); ?>",
          "useBoldText": true,
          "useBottomMargin": false,
          "class": "DepictionSubheaderView"
        },
         {
          "title": "<?php echo(nl2br($package_info['Source']));?>", "useBoldText": true,
          "useBottomMargin": false,
          "class": "DepictionSubheaderView"
        },
        
        {
          "itemCornerRadius": 6,
          "itemSize": "{<?php if(!empty($package_info['Size'])){echo(sizeext($package_info['Size']));} ?>, 284.586666666666664}",
     	"screenshots": [
	{
	"accessibilityText": "Screenshot",
	"url": "<?php echo($screenshot); ?>?raw=true"
	}
	],
	"class": "DepictionScreenshotsView"
	},
	{
	"markdown": "Other thing goes here",
	"useSpacing": true,
	"class": "DepictionMarkdownView"
	},
        {
          "markdown": "",
          "useSpacing": true,
          "class": "DepictionMarkdownView"
        },
        {
          "class": "DepictionSeparatorView"
        },
        {
          "title": "Latest Version",
          "class": "DepictionHeaderView"
        },
        {
          "title": "Version",
          "text": "<?php echo($package_info['Version']); ?>",
          "class": "DepictionTableTextView"
        },
        {
          "title": "Released",
          "text": "<?php echo($package_info['CreateStamp']); ?>",
          "class": "DepictionTableTextView"
        },
        {
          "title": "Price",
          "text": "Free",
          "class": "DepictionTableTextView"
        },
        {
          "spacing": 8,
          "class": "DepictionSpacerView"
        },
        {
          "title": "Developer",
          "text": "<?php echo($package_info['Author']); ?>",
          "class": "DepictionTableTextView"
        },
        {
          "title": "Paypal",
          "action": "https://www.paypal.me/kahil626",
          "class": "DepictionTableButtonView"
        },
        {
          "title": "Twitter",
          "action": "https://www.twitter.com/AzozzALFiras",
          "class": "DepictionTableButtonView"
        },
        {
          "title": "Snapchat",
          "action": "https://www.snapchat.com/add/n.uf",
          "class": "DepictionTableButtonView"
        },
        {
          "title": "instagram",
          "action": "https://www.instagram.com/_fkn",
          "class": "DepictionTableButtonView"
        }
      ],
      "class": "DepictionStackView"
    },
    {
      "tabname": "Changelog",
      "views": [
        {
          "title": "What's new ",
          "useBoldText": "",
          "useBottomMargin": true,
          "class": "DepictionSubheaderView"
        },
        {
          "markdown": "Coming Soon",
          "useRawFormat": true,
          "class": "DepictionMarkdownView"
        }
      ],
      "class": "DepictionStackView"
    }
  ],
  "class": "DepictionTabView"
}

<?php }?>



```


# Changelog


```json

{
          "title": "What's new ",
          "useBoldText": "",
          "useBottomMargin": true,
          "class": "DepictionSubheaderView"
        },

```


# Tweak Information


```json

{
          "title": "<?php echo($package_info['Name']); ?>",
          "useBoldText": true,
          "useBottomMargin": false,
          "class": "DepictionSubheaderView"
        },
```


# header Image


```json

{
  "minVersion": "0.1",
  "headerImage": "https://pbs.twimg.com/profile_banners/3686220322/1553719342/1500x500",
```

# Then go to the database
# phpMyAdmin


- Then the rule for the repo
- Then ```apt_Packages```
- Then ```Structure```
- Change the name of one of the blank tables 
- for example ```Enhances```
- Change its name to ```SileoDepiction```
- Then go to folder manage
- Fill in the following file's ```build.php``` & ```output.php```  
- Look for  ```Enhances```
- Change it to ```SileoDepiction```
- - Fill in the following file ```edit.php``` 

# Add the following code

```php

		<input  type="text" style="width: 400px;" name="SileoDepiction" value="https://yourwebiste/sileo.php?pid=<?php echo $request_id;?>"/>


```

# After that you must be working well and you have to update your tweaks

# Azozz ALFiras

# You can make your own source through

https://github.com/dlxg/WEIPDCRM  or https://xRepo.co
















