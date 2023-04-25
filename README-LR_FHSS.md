# Notes for LR-FHSS Support

At this point, the setup of Chirpstack v4 in general or Chirpstack v4 docker image (in this reposistory) *MAY* not fully support LR-FHSS packets/devices/gateways. Further checking, consideration & modification should be conducted while deploying the server. Below are some notes that may help you config your server for approriate use-cases.

## Region extra channels

The first thing that should be put into consideration is *Region extra channels*. This can be defined in the corresponding toml region file located in [configuration/chirpstack/](configuration/chirpstack/) folder. For example, there are 8 additional LR-FHSS channel added to EU868 configuration file. The datarates for EU868 are indexed from 8 to 11.

For other (your) frequency plan, the configuration should be the same. However, the minimum/maximum datarate (```min_dr``` and ```max_dr```) could be indexed differently. Refer to the original [Chirpstack region datarate define](https://github.com/chirpstack/chirpstack/tree/master/lrwn/src/region) for this information. Look for ```DataRateModulation::LrFhss``` in the ```<region>.rs``` file for this information. I left the example for EU868 as follow.

*LR-FHSS Additional channels added to EU868*:

```
# LR_FHSS
[[regions.network.extra_channels]]
frequency=867200000
min_dr=8
max_dr=11

[[regions.network.extra_channels]]
frequency=867400000
min_dr=8
max_dr=11

[[regions.network.extra_channels]]
frequency=867600000
min_dr=8
max_dr=11

[[regions.network.extra_channels]]
frequency=867800000
min_dr=8
max_dr=11

[[regions.network.extra_channels]]
frequency=868000000
min_dr=8
max_dr=11

[[regions.network.extra_channels]]
frequency=868200000
min_dr=8
max_dr=11

[[regions.network.extra_channels]]
frequency=868600000
min_dr=8
max_dr=11
```

*LR-FHSS Datarate define* (the original file can be found [here](https://github.com/chirpstack/chirpstack/blob/master/lrwn/src/region/eu868.rs)):

```
(
    8,
    DataRate {
        uplink: true,
        downlink: false,
        modulation: DataRateModulation::LrFhss(LrFhssDataRate {
            coding_rate: "2/6".to_string(),
            occupied_channel_width: 137000,
        }),
    },
),
(
    9,
    DataRate {
        uplink: true,
        downlink: false,
        modulation: DataRateModulation::LrFhss(LrFhssDataRate {
            coding_rate: "4/6".to_string(),
            occupied_channel_width: 137000,
        }),
    },
),
(
    10,
    DataRate {
        uplink: true,
        downlink: false,
        modulation: DataRateModulation::LrFhss(LrFhssDataRate {
            coding_rate: "2/6".to_string(),
            occupied_channel_width: 336000,
        }),
    },
),
(
    11,
    DataRate {
        uplink: true,
        downlink: false,
        modulation: DataRateModulation::LrFhss(LrFhssDataRate {
            coding_rate: "4/6".to_string(),
            occupied_channel_width: 336000,
        }),
    },
)
```

## Gateway uplink Coding Rate

Currently, Chirpstack Gateway Bridge are defined valid Coding Rate for LR-FHSS as followings. If you are using Packet Forwarders in your Gateway, make sure that you are sending a valid ```CodR``` in uplink JSON. In my case, it's Semtech Packet Forwarder and Semtech define ```CodingRate 1/2``` & ```CodingRate 1/3``` instead of ```CodingRate 2/4``` & ```CodingRate 2/6``` as Chirpstack did. This will lead to the **"invalid CodR"** error.

If you are encounting the same issue, you may want to modify your Packet Forwarder JSON format. It's easier than modifying Chirpstack source code (at lease in my case).

```
switch rxpk.CodR {
	case "4/5":
		cr = gw.CodeRate_CR_4_5
	case "4/6":
		cr = gw.CodeRate_CR_4_6
	case "4/7":
		cr = gw.CodeRate_CR_4_7
	case "4/8":
		cr = gw.CodeRate_CR_4_8
	case "3/8":
		cr = gw.CodeRate_CR_3_8
	case "2/6":
		cr = gw.CodeRate_CR_2_6
	case "1/4":
		cr = gw.CodeRate_CR_1_4
	case "1/6":
		cr = gw.CodeRate_CR_1_6
	case "5/6":
		cr = gw.CodeRate_CR_5_6
	case "4/5LI":
		cr = gw.CodeRate_CR_LI_4_5
	case "4/6LI":
		cr = gw.CodeRate_CR_LI_4_6
	case "4/8LI":
		cr = gw.CodeRate_CR_LI_4_8
	default:
		return &frame, errors.New(fmt.Sprintf("backend/semtechudp:packets: invalid CodR: %s", rxpk.CodR))
	}
  ```

*If you want my precompile binary supporing LR-FHSS for Gateways, sorry I cannot share it now. Contact Semtech instead!*