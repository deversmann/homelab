# Home Networks

## VLANS

| VLAN ID | VLAN Name            | Default Domain   |
| ------: | -------------------- | ---------------- |
|       1 | Management           | eversmann.xyz    |
|      10 | Trusted              | eversmann.haus   |
|      20 | IoT                  | eversmann.foo    |
|      30 | Streaming            | eversmann.stream |
|      40 | SNO                  | sno-ball.net     |
|      50 | Lab                  | eversmann.dev    |
|      99 | Guest                |                  |
|       X | External VPC Tunnel  | eversmann.games  |

## Network Table

| System        | Patch | UDMSE | USW24 | IWHD(Deacon) | IWHD(Zach) | VLAN    | Speed |
| :------------ | :---- | :---- | :---- | :----------- | :--------- | :------ | :---- |
|               |       | 1     |       |              |            | 99      | 1G    |
|               |       | 2     |       |              |            | 99      | 1G    |
|               |       | 3     |       |              |            | 99      | 1G    |
|               |       | 4     |       |              |            | 99      | 1G    |
|               |       | 5     |       |              |            | 99      | 1G    |
|               |       | 6     |       |              |            | 99      | 1G    |
|               |       | 7     |       |              |            | 99      | 1G    |
|               |       | 8     |       |              |            | 99      | 1G    |
| xfinity       | 24    | 9     |       |              |            | up(WAN) | 2.5G  |
|               |       | 10    |       |              |            | 99      | 10G   |
| USW24(25)     |       | 11    |       |              |            | 99      | 10G   |
| R420(1)       | 4     |       | 1     |              |            | 50      | 1G    |
| R420(iDRAC)   | 6     |       | 2     |              |            | 50      | 1G    |
|               |       |       | 3     |              |            | 50      | 1G    |
|               |       |       | 4     |              |            | 50      | 1G    |
| R720(1)       | 7     |       | 5     |              |            | 50      | 1G    |
| R720(iDRAC)   | 9     |       | 6     |              |            | 50      | 1G    |
| R720(2)       | 8     |       | 7     |              |            | 50      | 1G    |
|               |       |       | 8     |              |            | 50      | 1G    |
| R730(1)       | 10    |       | 9     |              |            | 50      | 1G    |
| R730(iDRAC)   | 12    |       | 10    |              |            | 50      | 1G    |
| R730(2)       | 11    |       | 11    |              |            | 50      | 1G    |
|               |       |       | 12    |              |            | 50      | 1G    |
|               |       |       | 13    |              |            | 99      | 1G    |
|               |       |       | 14    |              |            | 99      | 1G    |
| U7PRO XG      | 16    |       | 15    |              |            | 1       | 1G    |
|               |       |       | 16    |              |            | 99      | 1G    |
| nas-1         | 17    |       | 17    |              |            | 10      | 1G    |
| infra-1(1)    | 18    |       | 18    |              |            | 10      | 1G    |
| infra-1(2)    | 19    |       | 19    |              |            | 10      | 1G    |
| infra-1(3)    | 20    |       | 20    |              |            | 10      | 1G    |
| IWHD(Deacon)  | 21    |       | 21    |              |            | 1       | 1G    |
| IWHD(Zach)    | 22    |       | 22    |              |            | 1       | 1G    |
| lunchbox      |       |       | 23    |              |            | 40      | 1G    |
| lunchbox(BMC) |       |       | 24    |              |            | 40      | 1G    |
| UDMSE(11)     |       |       | 25    |              |            | up      | 10G   |
|               |       |       | 26    |              |            | 99      | 10G   |
| Deacon's PC   |       |       |       | 1            |            | 10      | 1G    |
|               |       |       |       | 2            |            | 10      | 1G    |
|               |       |       |       | 3            |            | 10      | 1G    |
|               |       |       |       | 4            |            | 10      | 1G    |
| USW24(21)     |       |       |       | 5            |            | up      | 1G    |
|               |       |       |       |              | 1          | 10      | 1G    |
| Zach's PC     |       |       |       |              | 2          | 10      | 1G    |
|               |       |       |       |              | 3          | 10      | 1G    |
|               |       |       |       |              | 4          | 10      | 1G    |
| USW24(22)     |       |       |       |              | 5          | up      | 1G    |


[↩️ Back to the README](../README.md)
