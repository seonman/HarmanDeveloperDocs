Getting Started Guide (iOS)
===========================

HKWirelessHD SDK supports both Objective-C and Swift. This document assumes that developer creates his/her app using the Swift language.

There are two versions of SDK
- a normal version: HKWirelessHDSDK
- a lightweight version: HKWirelessHDSDKlw

Most of the features are common in the two version. The only difference is that the normal version includes an API for playing web streaming audio, while the lightweight version does not.

The reason we support the SDK as two separate versions is that we know that many developers want APIs for web streaming. To support this feature, we had to include a version of FFMPEG library inside of the SDK library. But, some developers may want to use their own version of MMPEG to handle audio stream for their own particular purpose. We don't want to discourage developers to use any open source libraries along with our SDK.

Please see the descriptions of each version below and make a proper choice for your app.

- HKWirelessHD (normal version)
	- Support web streaming music playback (streaming music from HTTP server, etc.)
	- libz.dylib and libbz2.dylib are required when linking.
	
- HKWirelessHDlw (lightweight version)
	- **Do not support web streaming music playback**
	- No other library required
		
So, if you do not need web streaming music playback for your app, you may use HKWirelessHDlw (lightweight) version. Otherwise, you should use HHWirelessHD version.


In the section, we will use HKWSimple app as an example of using the normal version and HKPage app as an example of using the lightweight version.


Project Setup with HKWirelessHDSDKlw (lightweight version)
-----------------------------------------------------------

Include HKWirelessHDSDKlw into your project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Add *HKWirelessHDSDKlw* to your project by dragging and dropping the HKWirelessHDSDKlw folder into the project navigator.
- When you get a dialog saying *Choose options for adding these files* :,
	- Check the checkmark of *Copy items if needed*
	- Select *Create groups*, and click finish.
- By doing this, the include headers and libraries for HKWirelessHDSDKlw are added to your project. 

Make sure if libHKWirelessHDlw.a was added to your *Link Binary With Libraries*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Phases > Link Binary With Libraries 
	- Check if *libHKWirelessHDlw.a* was added to the list.
	- If it was not added, add it by clicking *+* and *Add Other...*.

After adding the HKWirelessHDSDKlw folder into your project and adding libHKWirelessHD.a, the project navigator will look like as below:

.. figure:: img/getting-started-iOS/project-setting-lw-1.png

Add Swift Bridging Header
~~~~~~~~~~~~~~~~~~~~~~~~~~~

HKWirelessHD SDK has been written in C++ and Objective-C. Therefore, when you use the SDK in Swift, you should include Swift Bridging Header in your project. To do this:

1. Go to Project Setting > Build Setting > Swift Compiler - Code Generation > Objective-C Bridging Header

.. figure:: img/getting-started-iOS/project-setting-3.png

2. Add [My-Project-Name]/[My-Project-Name]-Bridging-Header.h

	- In our example, it looks like : HKPage/HKPage-Bridging-Header.h

3. Add the following lines in the header file.

.. code-block:: swift

	#import "HKWControlHandler.h"
	#import "HKWPlayerEventHandlerSingleton.h"
	#import "HKWDeviceEventHandlerSingleton.h"

Add -lstdc++ as linker flag
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Settings > Linking > Other Linker Flags
	- Add “-lstdc++” in the text field

.. figure:: img/getting-started-iOS/project-setting-4.png

Project Setup with HKWirelessHDSDK (normal version)
-----------------------------------------------------------

Include HKWirelessHDSDK into your project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Add *HKWirelessHDSDK* to your project by dragging and dropping the HKWirelessHDSDK folder into the project navigator.
- When you get a dialog saying *Choose options for adding these files* :,
	- Check the checkmark of *Copy items if needed*
	- Select *Create groups*, and click finish.
- By doing this, the include headers and libraries for HKWirelessHDSDK are added to your project. 

Make sure if libHKWirelessHD.a was added to your *Link Binary With Libraries*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Phases > Link Binary With Libraries 
	- Check if *libHKWirelessHDlw.a* was added to the list.
	- If it was not added, add it by clicking '+' and *Add Other...*.

Add libz.dylib and libbz.dylib to your *Link Binary With Libraries*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Phases > Link Binary With Libraries 
	- Click '+' and find and add *libz.dylib* and *libbz2.dylib* to the list

After adding the HKWirelessHDSDKw folder into your project and adding libHKWirelessHD.a, libz.dylib and libbz2.dylib, the project navigator will look like as below:

.. figure:: img/getting-started-iOS/project-setting-normal.png

Add Swift Bridging Header and -lstdc++ as linker flag
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Follow the instruction for adding Swift bridging header and -lstdc++ linker flag as described in the previous section.

Creating a Sample Application (HKWSimple)
-------------------------------------------

In this section, we explain how to create a HKWirelessHD iOS App. We will create a simple iOS app called **HKWSimple** that can play WAV or MP3 file, and also play Web-based streaming music with HTTP protocol. 

This app is so simple, so we hightly recommend you to start with this app to understand how HKWirelessHD is working.

As shown in the figure, the app is composed of a sequence of UIViewController starting from a TableViewController showing a list of available speakers, and then a TableViewController showing a list of songs to play, and then finally a ViewController that shows a playback control panel with Play/Stop buttons and Volume control buttons.

.. figure:: img/getting-started-iOS/hkwsimple-1.png

1. Project Setup
~~~~~~~~~~~~~~~~~

For the project setup, please refer to the previous session of **Project Setup with HKWirelessHDSDK (normal version)**.

2. Initialize HKWirelessHD Controller
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In HKWSimple app, the initialization of HKWirelessHD Controller is done in the first ViewController called MainVC. When the app is launched, if HKWControlHandler is not initialized, then the app shows a dialog saying it is about to initialize the HKWControlHandler. This is done in ``viewDidLoad()``. After that, in ``viewDidAppear()``, the app actually tries to initialize HKWControlHandler. And it is successful, it dismisses the dialog. If not, it keeps showing the dialog so that the user can take an action.

.. code-block:: swift

	class MainVC: UIViewController {
		var g_alert: UIAlertController!
		
		override func viewDidLoad() {
			super.viewDidLoad()
			
			if !HKWControlHandler.sharedInstance().isInitialized() {
				// show the network initialization dialog
				println("show dialog")
				g_alert = UIAlertController(title: "Initializing", message: "If this dialog does not disappear, please check if any other HK WirelessHD App is running on the phone and kill it. Or, your phone is not in a Wifi network.", preferredStyle: .Alert)
				self.presentViewController(g_alert, animated: true, completion: nil)
			}
		}

		override func viewDidAppear(animated: Bool) {
			if !HKWControlHandler.sharedInstance().initializing() && !HKWControlHandler.sharedInstance().isInitialized() {
				dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), {
					if HKWControlHandler.sharedInstance().initializeHKWirelessController(kLicenseKeyGlobal) != 0 {
						println("initializeHKWirelessControl failed : invalid license key")
                    	return
                	}
                	println("initializeHKWirelessControl - OK");
                
                	// dismiss the network initialization dialog
                	if self.g_alert != nil {
						self.g_alert.dismissViewControllerAnimated(true, completion: nil)
                	}
				})
			}
		}
	}


3. Get the list of available speakers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The list of speakers are presented in ``SpeakerSelectionTVC`` TableViewController. It should receive the event about the device status, so it should implement the functions of ``HKWDeviceEventHandelrDelegate``. First, the ``SpeakerSelectionTVC`` class should have ``HKWDeviceEventHandlerDelegate`` in its class declaration.

.. code-block:: swift

	class SpeakerSelectionTVC: UITableViewController, HKWDeviceEventHandlerDelegate {

In ``viewDidLoad()``, the class will set the ``delegate`` of HKWDeviceEventHandler instance as itself. And then, it starts to refresh the device information, by calling ``startRefreshDeviceInfo()``.

.. code-block:: swift

		override func viewDidLoad() {
			super.viewDidLoad()
			HKWDeviceEventHandlerSingleton.sharedInstance().delegate = self
			HKWControlHandler.sharedInstance().startRefreshDeviceInfo()
		}

If the SpeakerSelectionTVC disappears, for example, by clicking **Back** button of Navigation Controller, it should stop refreshing the device info, so it calls ``stopRefreshDeviceInfo()`` in ``viewDidDisappear()``.

.. code-block:: swift
    
		override func viewDidDisappear(animated: Bool) {
			super.viewDidDisappear(animated)
			HKWControlHandler.sharedInstance().stopRefreshDeviceInfo()
		}

The follow codes are all about listing the speakers with their detailed information in the TableView. If a speaker is active, that is, the speaker belongs to the current session, then it checks the checkmark of the cell.

.. code-block:: swift
    

		override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
			return HKWControlHandler.sharedInstance().getGroupCount()
		}
			
		override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
			return HKWControlHandler.sharedInstance().getDeviceCountInGroupIndex(section)
		}
			
		override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
			let cell = tableView.dequeueReusableCellWithIdentifier("Speaker_Cell", forIndexPath: indexPath) as! UITableViewCell
			cell.selectionStyle = UITableViewCellSelectionStyle.None
			var deviceInfo: DeviceInfo = HKWControlHandler.sharedInstance().getDeviceInfoByGroupIndexAndDeviceIndex(indexPath.section, deviceIndex: indexPath.row)
			cell.textLabel?.text = deviceInfo.deviceName;
			var uniqueId: NSString = NSString(format: "ID:%llu, Vol:%d", deviceInfo.deviceId, deviceInfo.volume)
			cell.detailTextLabel?.text = uniqueId as String
			
			// Show the checkmark if the speaker is active
			if deviceInfo.active {
				cell.accessoryType = UITableViewCellAccessoryType.Checkmark
			} else {
				cell.accessoryType = UITableViewCellAccessoryType.None
			}
			return cell
		}
				
		override func tableView(tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
			var header = HKWControlHandler.sharedInstance().getDeviceGroupNameByIndex(section);
			return header
		}

		override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
			let cell = tableView.dequeueReusableCellWithIdentifier("Speaker_Cell", forIndexPath: indexPath) as! UITableViewCell
			var deviceInfo: DeviceInfo = HKWControlHandler.sharedInstance().getDeviceInfoByGroupIndexAndDeviceIndex(indexPath.section, deviceIndex: indexPath.row)
			if deviceInfo.active {
				HKWControlHandler.sharedInstance().removeDeviceFromSession(deviceInfo.deviceId)
				cell.accessoryType = UITableViewCellAccessoryType.Checkmark
			} else {
				HKWControlHandler.sharedInstance().addDeviceToSession(deviceInfo.deviceId)
				cell.accessoryType = UITableViewCellAccessoryType.None
			}
		}

The follow codes are for handling events from Device Handler. In this example, it just redraw the table when it receives any device update events from the HKWControlHandler.

.. code-block:: swift
					
		func hkwDeviceStateUpdated(deviceId: Int64, withReason reason: Int) {
			self.tableView.reloadData()
		}
				
		func hkwErrorOccurred(errorCode: Int, withErrorMessage errorMesg: String!) {
			println("Error: \(errorMesg)")
		}
	}

The following figure shows a screen of the speaker list.

.. figure:: img/getting-started-iOS/speaker-list.png
	:scale: 40

4. Create the playlist to play
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``SongSelectionTVC`` shows the list of songs availabe for playback. It searches for the songs included in the app as bundle, and show the list of the songs. And also it adds the songs for web-based streaming.

.. code-block:: swift

	class SongSelectionTVC: UITableViewController {
	    var g_wavFiles = [String]()
	    var g_mp3Files = [String]()
	    var curSection = 0
	    var curRow = 0
	    let serverUrlPrefix = "http://seonman.github.io/music/";
	    var songList = ["ec-faith.wav", "hyolyn.mp3"]
	    @IBOutlet var bbiNowPlaying: UIBarButtonItem!
    
	    override func viewDidLoad() {
	        super.viewDidLoad()
        
	        var bundleRoot = NSBundle.mainBundle().bundlePath
	        var dirContents: NSArray = NSFileManager.defaultManager().contentsOfDirectoryAtPath(bundleRoot, error: nil)!
	        var fltr: NSPredicate = NSPredicate(format: "self ENDSWITH '.wav'")
	        g_wavFiles = dirContents.filteredArrayUsingPredicate(fltr) as! [String]
        
	        for var i = 0; i < g_wavFiles.count; i++ {
	            println("wav file: \(g_wavFiles[i])")
	        }
        
	        var fltr2: NSPredicate = NSPredicate(format: "self ENDSWITH '.mp3'")
	        g_mp3Files = dirContents.filteredArrayUsingPredicate(fltr2) as! [String]
        
	        for var i = 0; i < g_mp3Files.count; i++ {
	            println("mp3 file: \(g_mp3Files[i])")
	        }
        
	        bbiNowPlaying.enabled = HKWControlHandler.sharedInstance().isPlaying()
	    }

	    override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
	        return 3
	    }

	    override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	        if section == 0 {
	            return g_wavFiles.count
	        } else if section == 1 {
	            return g_mp3Files.count
	        } else if section == 2 {
	            return songList.count
	        }else {
	            return 0
	        }
	    }

	    override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
	        let cell = tableView.dequeueReusableCellWithIdentifier("SongTitle_Cell", forIndexPath: indexPath) as! UITableViewCell
	        if indexPath.section == 0 {
	            cell.textLabel?.text = g_wavFiles[indexPath.row]
	        } else if indexPath.section == 1 {
	            cell.textLabel?.text = g_mp3Files[indexPath.row]
	        } else {
	            cell.textLabel?.text = songList[indexPath.row]
	        }
	        return cell
	    }
    
	    override func tableView(tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
	        if section == 0 {
	            return "WAV file"
	        } else if section == 1 {
	            return "MP3 file"
	        }else {
	            return "Web Streaming"
	        }
	    }

	    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
	        if segue.identifier == "Song_Cell" {
	            let section = self.tableView.indexPathForSelectedRow()?.section
	            curSection = section!
	            let row = self.tableView.indexPathForSelectedRow()?.row
	            curRow = row!
            
	            let destTVC:NowPlayingVC = segue.destinationViewController as! NowPlayingVC
	            destTVC.section = curSection
	            destTVC.row = curRow
	            if curSection == 0 {
	                destTVC.songTitle = g_wavFiles[curRow]
	            } else if curSection == 1 {
	                destTVC.songTitle = g_mp3Files[curRow]
	            } else {
	                destTVC.songTitle = songList[curRow]
	                destTVC.songUrl = serverUrlPrefix + songList[curRow]
	                destTVC.serverUrl = serverUrlPrefix
	            }
            
	            destTVC.viewLoadByCellSelection = true
	            destTVC.nsWavPath = NSBundle.mainBundle().bundlePath.stringByAppendingPathComponent(destTVC.songTitle)
	            destTVC.songSelectionTVC = self
	        }
	        else if segue.identifier == "NowPlaying_BBI" {
	            let destTVC:NowPlayingVC = segue.destinationViewController as! NowPlayingVC
	            if curSection == 0 {
	                destTVC.songTitle = g_wavFiles[curRow]
	            } else if curSection == 1 {
	                destTVC.songTitle = g_mp3Files[curRow]
	            } else {
	                destTVC.songTitle = songList[curRow]
	                destTVC.songUrl = serverUrlPrefix + songList[curRow]
	                destTVC.serverUrl = serverUrlPrefix
	            }
            
	            destTVC.viewLoadByCellSelection = false
	            destTVC.nsWavPath = NSBundle.mainBundle().bundlePath.stringByAppendingPathComponent(destTVC.songTitle)
	            destTVC.songSelectionTVC = self

	        }
	    }
	}

The following figure shows an example of the SongSelectionTVC screen.

.. figure:: img/getting-started-iOS/song-list.png
	:scale: 40


5. Playback and Volume Control
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``NowPlayingVC`` controls the playback and volume level. To receive the events about playback, it must implement ``HKWPlayerEventHandlerDelegate``, and set the delegate value as itself.

.. code-block:: swift

	class NowPlayingVC: UIViewController, HKWPlayerEventHandlerDelegate {
	    var row = 0
	    var section = 0
	    var songTitle = ""
	    var nsWavPath = ""
	    var viewLoadByCellSelection = false
	    var songSelectionTVC: SongSelectionTVC!
	    var curVolume:Int = 50
	    var songUrl = ""
	    var serverUrl = ""
	    var g_alert: UIAlertController!
	    @IBOutlet var labelSongTitle: UILabel!
	    @IBOutlet var btnPlayStop: UIButton!
	    @IBOutlet var labelAverageVolume: UILabel!
	    @IBOutlet var btnVolumeDown: UIButton!
	    @IBOutlet var btnVolumeUp: UIButton!
	    @IBOutlet var labelStatus: UILabel!
    
	    override func viewDidLoad() {
	        super.viewDidLoad()
    
	        HKWPlayerEventHandlerSingleton.sharedInstance().delegate = self

	        labelSongTitle.text = songTitle
	        curVolume = HKWControlHandler.sharedInstance().getVolume()
	        labelAverageVolume.text = "Volume: \(curVolume)"
    
	        if viewLoadByCellSelection {
	            playCurrentTitle()
        
	        } else {
	            if HKWControlHandler.sharedInstance().isPlaying() {
	                btnPlayStop.setTitle("Stop", forState: UIControlState.Normal)
	                labelStatus.text = "Now Playing"
	            }
	            else {
	                btnPlayStop.setTitle("Play", forState: UIControlState.Normal)
	                labelStatus.text = "Play Stopped"
	            }
	        }
	    }
		
	    @IBAction func playOrStop(sender: UIButton) {
	        if HKWControlHandler.sharedInstance().isPlaying() {
	            HKWControlHandler.sharedInstance().pause()
	            labelStatus.text = "Play Stopped"
	            btnPlayStop.setTitle("Play", forState: UIControlState.Normal)
	        }
	        else {
	            playCurrentTitle()
	            labelStatus.text = "Now Playing"
	        }
	    }
    
	    @IBAction func volumeUp(sender: UIButton) {
	        curVolume += 5
	        if curVolume > 50 {
	            curVolume = 50
	        }
	        HKWControlHandler.sharedInstance().setVolume(curVolume)
	        labelAverageVolume.text = "Volume: \(curVolume)"
	    }

	    @IBAction func volumeDown(sender: UIButton) {
	        curVolume -= 5
	        if curVolume < 0 {
	            curVolume = 0
	        }
	        HKWControlHandler.sharedInstance().setVolume(curVolume)
	        labelAverageVolume.text = "Volume: \(curVolume)"
	    }
    
	    func playCurrentTitle() {
	        // just to be sure that there is no running playback
	        HKWControlHandler.sharedInstance().stop()
        
	        println("nsWavPath: \(nsWavPath)")
	        if section == 0 {
	            if HKWControlHandler.sharedInstance().playWAV(nsWavPath) {
	                // now playing, so change the icon to "STOP"
	                btnPlayStop.setTitle("Stop", forState: UIControlState.Normal)
	            }
	        } else if section == 1 {
	            let assetUrl = NSURL(fileURLWithPath: nsWavPath)
	            if HKWControlHandler.sharedInstance().playCAF(assetUrl, songName: songTitle, resumeFlag: false) {
	                // now playing, so change the icon to "STOP"
	                btnPlayStop.setTitle("Stop", forState: UIControlState.Normal)
	            }
	        } else {
	            playStreaming()
	        }
	        songSelectionTVC.bbiNowPlaying.enabled = true
	    }
    
	    func playStreaming() {
	        HKWControlHandler.sharedInstance().playStreamingMedia(songUrl, withCallback: {(bool result) -> Void in
	            if result == false {
	                println("playStreamingMedia: failed")
	                self.btnPlayStop.selected = false
	                self.g_alert = UIAlertController(title: "Warning", message: "Playing streaming media failed. Please check the Internet connection or check if the meida URL is correct.", preferredStyle: .Alert)
	                self.g_alert.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.Default, handler: nil))
	                self.presentViewController(self.g_alert, animated: true, completion: nil)
	            } else {
	                println("playStreamingMedia: successful")
	                self.btnPlayStop.setTitle("Stop", forState: UIControlState.Normal)
	            }
	        })
	    }
    
	    func hkwPlayEnded() {
	        btnPlayStop.setTitle("Play", forState: UIControlState.Normal)
	        songSelectionTVC.bbiNowPlaying.enabled = false
	    }

	    func hkwDeviceVolumeChanged(deviceId: Int64, deviceVolume: Int, withAverageVolume avgVolume: Int) {
	        println("avgVolume: \(avgVolume)")
	        curVolume = avgVolume
	    }
	}
