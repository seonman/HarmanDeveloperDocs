==========================================
JBL Pulse2 iOS Framework Developer’s Guide
==========================================

:Version: 1.0

Prerequirements
---------------

The following steps should be done by Developer before start working with SDK:

-  Add the Pulse2SDK framework to Linked Frameworks and Libraries

-  Import API header file “HMNPulse2API.h”

-  Import Apple ExternalAccessory.framework to Xcode project

-  Add protocol string “com.jbl.connect” in the UISupportedExternalAccessoryProtocols section on the Info.plist

iPhone should esbablish BT connection with Pulse 2 Device in iPhone-> Settings -> Bluetooth.


===============
API Description
===============


Device General API methods
--------------------------

Application should be first establish BT connection with Device using **connectToMasterDevice**  function  (HMNDeviceGeneral.h). 

The notification EVENT\_DEVICE\_CONNECTED (HMNCommonDefs.h) will be received if connection established successfully. Other API functions can be used after this step.

The notification EVENT\_DEVICE\_CONNECTED includes dictionary with the following keys (defined in HMNDeviceGeneral.h):

-  KEY\_IAP\_CONNECTION\_ID;

-  KEY\_IAP\_MANUFACTURER;

-  KEY\_IAP\_NAME;

-  KEY\_IAP\_SERIAL\_NUMBER;

The notification EVENT\_DEVICE\_DISCONNECTED (HMNCommonDefs.h) will be received if connection with device lost.


Device Info API methods
-----------------------


/\*\*

\* @discussion Change device name.

\* @param deviceName An new device name. Nonnull.

\* @return none.

\*/

+ (void) **setDeviceName** :(NSString \* \_Nonnull)deviceName;

/\*\*

\* @discussion Request information from Device like name, battery, active channel.

\* @return none.

\*/

+ (void) **requestDeviceInfo**;

Application will receive notification “EVENT\_DEVICE\_INFO” with Dictionary included device info. The following keys can present in dictionary (HMNDeviceInfo.h):

-  KEY\_DEVICE\_INFO\_DEVICE\_INDEX;

-  KEY\_DEVICE\_INFO\_DEVICE\_NAME;

-  KEY\_DEVICE\_INFO\_PRODUCT\_ID;

-  KEY\_DEVICE\_INFO\_MODEL\_ID;

-  KEY\_DEVICE\_INFO\_BATTERY\_IS\_CHARGING;

-  KEY\_DEVICE\_INFO\_BATTERY\_VALUE;

-  KEY\_DEVICE\_INFO\_LINKED\_DEVICE\_COUNT;

-  KEY\_DEVICE\_INFO\_ACTIVE\_CHANNEL\_VALUE;

-  KEY\_DEVICE\_INFO\_AUDIO\_SOURCE\_VALUE;

-  KEY\_DEVICE\_INFO\_MAC\_ADDRESS\_VALUE;

/\*\*

\* @discussion Request information from Device for the specified token

\* @param token. Value from enumeration HMNToken

\* @param index. Device index: 0 - master, 1 - slave.

\* @return none.

\*/

+ (void) **requestDeviceInfoToken** :(HMNToken)token forDeviceIndex:(UInt8)index;

DFU API methods
---------------

/\*\*

\* @discussion Request version from Device.

\* @return none.

\*/

+ (void) **requestVersion**;

Application will receive notification “EVENT\_VERSION” with Dictionary included version info. The following keys can present in dictionary (HMNDFU.h):

KEY\_SW\_VERSION;

KEY\_HW\_VERSION;

Led Control API methods
-----------------------

/\*\*

\* @discussion Setup Background Color to Master Device

\* @param color - UIColor object

\* @param propageToSlaveDevice - Need to propagete background color to Slave device

\* @return none.

\*/

+ (void) **setBackgroundColor**:(UIColor \* \_Nonnull)color propagateToSlaveDevice:(BOOL)applyToSlave;

/\*\*

\* @discussion Draw 11(columns)x9(rows) color image bitmap on the Master Device

\* @param imageMatrix - Array of 99 UIColor\* objects,The color of each pixel is represented color by UIColor.

\* @return none.

\*/

+ (void) **setColorImage**:(NSArray \* \_Nonnull)imageMatrix;

/\*\*

\* @discussion Display Char on master and slave device

\* @param charAsciiCode - Char ascii code - in hex; Supported ascii symbols: 0..9 A..Z ? ! $ + - = % \* / # " & ' ( ) , . : ; < > \` { \| } ~

\* @param charColor - Color of char;

\* @param backgroundColor - Background color;

\* @param applyToSlave - if need to propagate to slave device

\* @return none.

\*/

+ (void) **setLedChar**:(UInt8)charAsciiCode charColor:(UIColor \* \_Nonnull)charColor backgroundColor:(UIColor \* \_Nonnull)backgroundColor applyToSlaveDevice:(BOOL)applyToSlave;

/\*\*

\* @discussion Propagate Current Led Pattern on master to slave device

\* @return none.

\*/

+ (void) **propagateCurrentLedPattern**;

/\*\*

\* @discussion Query device led brightness.

\* It will reply with EVENT\_BRIGHTNESS notification

\* @return none.

\*/

+ (void) **requestLedBrightness**;

Application will receive notification “EVENT\_BRIGHTNESS” with Dictionary included brightness info. The following keys can present in dictionary (HMNLedControl.h):

-  KEY\_LED\_BRIGHTNESS

/\*\*

\* @discussion Set Display Led Brightness on master device

\* @param brightness - 0..255;

\* @return none.

\*/

+ (void) **setLedBrightness**:(UInt8)brightness;

/\*\*

\* @discussion Set Pattern on master device

\* @param pattern - id of pattern

\* @param patternData - array of 99 unsigned int values wrapped to NSNumbers for Canvas and Firefly:

\* 0 - do not draw point

\* non-0 - draw point on canvas

\* - nil for other pattern types

\* @return none.

\*/

+ (void) **setLedPattern**:(HMNPattern)pattern withData:(NSArray \* \_Nullable)patternData;

/\*\*

\* @discussion Query device led pattern information.

\* It will reply with EVENT\_PATTERN\_INFO notification.

\* @return none.

\*/

+ (void) **requestLedPatternInfo**;

Application will receive notification “EVENT\_PATTERN\_INFO” with Dictionary included pattern info. The following keys can present in dictionary (HMNLedPattern.h):

KEY\_LED\_PATTERN\_ID its id of the pattern enumerated in the HMNCommonDefs.h

/// Device Patterns

typedef NS\_ENUM(NSInteger, HMNPattern) {

HMNPattern\_Firework = 0,

HMNPattern\_Traffic,

HMNPattern\_Star,

HMNPattern\_Wave,

HMNPattern\_FireFly,

HMNPattern\_Rain,

HMNPattern\_Fire,

HMNPattern\_Canvas,

HMNPattern\_Hourglass,

HMNPattern\_Ripple

};

Sensor Control API methods
--------------------------

/\*\*

\* @discussion Request microphone sound level from Device.

\* It will reply with EVENT\_SOUND notification

\* @return none.

\*/

+ (void) **requestMicrophoneSoundLevel**;

Application will receive notification “EVENT\_SOUND” with Dictionary included pattern info. The following key can present in dictionary (HMNSensorControl.h):

/// The Value has NSNumber value

KEY\_MICROPHONE\_LEVEL;

/\*\*

\* @discussion Request for capturing color by Color Picker from the Device

\* It will reply with EVENT\_SENSOR\_CAPTURE\_COLOR notification

\* @return none.

\*/

+ (void) **requestColorFromColorPicker**;

When device color sensor capture the color it send notification EVENT\_SENSOR\_CAPTURE\_COLOR.

Application will receive notification “EVENT\_SENSOR\_CAPTURE\_COLOR” with Dictionary included color info. The following keys can present in dictionary (HMNSensorControl.h):

KEY\_LED\_COLOR\_R;

KEY\_LED\_COLOR\_G;

KEY\_LED\_COLOR\_B;

Notes
-----

The device can accept one commad at second. If application required to send several commands it should send them with delay for 1 second for each command.

============
Sample Usage
============

Device Connection Example
-------------------------

(void) viewDidLoad {

	[super viewDidLoad];

	[self subscribeForNotification];

	[HMNDeviceGeneral connectToMasterDevice];

}

(void) subscribeForNotification {

	[[NSNotificationCenter defaultCenter] addObserver:self

	selector:@selector(deviceConnected)

	name:EVENT\_DEVICE\_CONNECTED

	object:nil];

}

Color Sensor handler example
----------------------------

(void) sensorCaptureColorReceived:(NSNotification \* )notification {

	NSDictionary \*devInfoDict = [notification userInfo];

	NSInteger R = [devInfoDict[KEY\_LED\_COLOR\_R] integerValue];

	NSInteger G = [devInfoDict[KEY\_LED\_COLOR\_G] integerValue];

	NSInteger B = [devInfoDict[KEY\_LED\_COLOR\_B] integerValue];

	NSLog(@"RGB color: %ld %ld %ld ", (long)R, (long)G, (long)B);

}

Device pattern handler example
------------------------------

(void) patternReceived:(NSNotification \* )notification {

	NSDictionary \*devInfoDict = [notification userInfo];

	HMNPattern pattern = [devInfoDict[KEY\_LED\_PATTERN\_ID] integerValue];

	NSLog(@"Pattern ID = %ld ", (long)pattern);

}

Version handler example
-----------------------

(void) versionReceived:(NSNotification \* )notification {

    NSDictionary \*devInfoDict = [notification userInfo];

    NSString \*swVersion = devInfoDict[KEY\_SW\_VERSION];

    NSString \*hwVersion = devInfoDict[KEY\_HW\_VERSION];

    NSLog(@"swVersion %@, hwVersion %@", swVersion, hwVersion);

    return;

}

Brightness handler example
--------------------------

(void) brightnessReceived:(NSNotification \* )notification {

    NSDictionary \*devInfoDict = [notification userInfo];

    NSUInteger brightness = [devInfoDict[KEY\_LED\_BRIGHTNESS] unsignedIntegerValue];

    NSLog(@"brightness %lu", (unsigned long)brightness);

    return;

}

Sound handler example
---------------------

(void) soundEventReceived:(NSNotification \* )notification {

    NSDictionary \*devInfoDict = [notification userInfo];

    NSUInteger microphoneLevel = [devInfoDict[KEY\_MICROPHONE\_LEVEL]     unsignedIntegerValue];

    NSLog(@"microphone level %lu", (unsigned long)microphoneLevel);

    return;

}

Show Letter example
-------------------

(void) showAChar {
    Byte charCode = 65; // A

    UIColor \*charColor = [UIColor redColor];

    UIColor \*backgroundColor =[UIColor blueColor];

    [HMNLedControl setLedChar:charCode charColor:charColor backgroundColor:backgroundColor applyToSlaveDevice:NO];

}

Show Hourglass example
----------------------

(void) showHourglassPattern {

    Byte imageMatrix[] = {

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

        0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0

	};

	NSMutableArray \*array = [NSMutableArray arrayWithCapacity:LED\_COUNT];
    for (int i=0; i<LED\_COUNT; i++) {

        [array addObject:[NSNumber numberWithUnsignedInt:imageMatrix[i]]];

    }

    [HMNLedControl setLedPattern:HMNPattern\_FireFly withData:array];

}

Show color image example
------------------------

(void)setDigit1ColorImage {

	Byte imageMatrix[] = {

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0,
	
		0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,

		0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0

	};


	NSMutableArray \*colors = [NSMutableArray arrayWithCapacity:LED\_COUNT];

	UIColor \*redColor = [UIColor redColor];

	UIColor \*blueColor = [UIColor blueColor];

	for (int i=0; i<LED\_COUNT; i++) {
		UIColor \*color = (imageMatrix[i] == 0)? redColor: blueColor;

		[colors addObject:color];

	}

    [HMNLedControl setColorImage:colors];

}
