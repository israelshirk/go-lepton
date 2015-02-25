go-lepton
=========

Serves images taken on a FLIR Lepton connected to a Raspberry Pi SPI port to
over HTTP.

![See how glass blocks IR](https://raw.github.com/maruel/go-lepton/master/cmd/lepton/static/photo_ir.png)


Prerequisite Setup
------------------

Setup is fairly involved so it's in its dedicated
[SETUP.md](https://github.com/maruel/go-lepton/blob/master/SETUP.md) page.


Installing
----------

Building go-lepton on the Raspberry Pi v1 takes ~10s which is slow but still
much faster than cross-compiling and transferring the file in.

    go get github.com/maruel/go-lepton/cmd/lepton

Install it as a crontab @reboot, e.g.:

    echo "su - $USER $GOPATH/src/github.com/maruel/go-lepton/run.sh" | sudo tee /root/start_lepton.sh
    sudo chmod +x /root/start_lepton.sh
    sudo crontab -e

Then add

    @reboot /root/start_lepton.sh


Verification
------------

Running the following command should have the corresponding output:

    $ lepton -query
    Status.CameraStatus: SystemReady
    Status.CommandCount: 0
    Serial:              0x12345
    Uptime:              48m56.275s
    Temperature:         30.75°C
    Temperature housing: 26.34°C
    Telemetry:           Enabled
    TelemetryLocation:   Header
    FCCMode.FFCShutterMode:          FFCShutterModeExternal
    FCCMode.ShutterTempLockoutState: ShutterTempLockoutStateInactive
    FCCMode.VideoFreezeDuringFFC:    Enabled
    FCCMode.FFCDesired:              Enabled
    FCCMode.ElapsedTimeSinceLastFFC: 48m56.285s
    FCCMode.DesiredFFCPeriod:        5m0s
    FCCMode.ExplicitCommandToOpen:   Disabled
    FCCMode.DesiredFFCTempDelta:     3.00°K
    FCCMode.ImminentDelay:           52


Performance
-----------

Reading the SPI port takes ~50% the CPU of a Raspberry Pi v1 running
Raspbian. There's a rumor about DMA based transfer but for now that's the
fastest that can be achieved.


Power
-----

The FLIR Lepton takes ~150mW. The breakout board doesn't expose the necessary
pins to put it in sleep mode. Sadly this means that if the Lepton goes into a
bad mode, rebooting the Pi won't help.
