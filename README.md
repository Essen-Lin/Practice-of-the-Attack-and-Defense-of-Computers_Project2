

## 電腦攻防實務 Project 2 CVE分析

### CVE分析1：CVE-2006-3146
DoS attack（Denial of Service attack）：利用一些攻擊放是來耗盡目標設備的資源或頻寬，以此讓其他使用者無法使用到目標設備提供的服務，若是兩台以上的攻擊電腦執行則稱為DDoS Attack（distributed denial-of-service attack)。

使用設備：
1. 紅外線藍牙耳/額溫槍 (可利用藍芽4.0 與手機相連）
2. 電腦（Ubuntu）: 執行BlueSmack Attack
3. 手機：Xperia XA2 Plus （可以透過裝置名稱尋找唯一的 MAC address)


參考資料：
[1] https://www.cybervie.com/blog/bluesmack-attack/


### CVE分析2：CVE-2020-10135
#### Master 跟Slave在配對(pairing)過程中主要可以分成三個階段。
Phase 1: Pairing Feature Exchange，透過phase1互相了解master還有slave之間互相可以配對需求與能力 (ex: 藍芽鍵盤可以使用鍵盤的input來配對顯示的數字)。 
Phase 2: 根據phase 1的feature exchange，決定要透過過legacy pairing或secure pairing將兩個藍芽裝置進行連接 (secure pairing 採用的配對方式較為安全)。
Phase 3: 經過phase 1和phase2之後，雙方已經產生了加密key，因而可以建立加密的連接。

#### BIAS Attacks on Legacy Secure Connection:
當Alice (slave)想與Bob (master)建立secure connection時，若使用legacy authticatoin [2]，此時需要Alice對Bob進行authentication；Bob也需要對Alice進行authentication，但藍芽規範中並未要求雙方皆須進行legacy authentication，因此Charlie可以假冒成Bob (master)，並不需要進行authentication。若Charlie想假冒成Alice (slave)則可以利用藍芽中的role switch，再完成前述步驟。

#### BIAS Downgrade on Secure Connection:
secure connection使用了比legacy secure connection更為安全的加密演算法，此外也提供了雙方須進行authentication的規範。但在規範中，secure connection並未要求雙方的背並需永遠使用secure connection。因此，Alice (slave)與Bob (master)在進行secure connection時，也可以使用legacy secure connection即可。而Charlie怎利用此方式來假冒Alice或者假冒Bob。

##### 參考資料：
[1] Antonioli, Daniele, Nils Ole Tippenhauer, and Kasper Rasmussen. "BIAS: bluetooth impersonation attacks." 2020 IEEE Symposium on Security and Privacy (SP). IEEE, 2020.

[2]  Bluetooth SIG. Bluetooth Core Specification v5.0. https://www.bluetooth.org/DocMan/handlers/D


### CVE分析3：CVE-2020-15802
#### 原機制正常運作原理: 
在藍芽執行的Basic Rate(BR)或Enhanced Data Rate(EDR)或者是Low Energy(LE)的模式下，會使用Cross-Transport Key Derivation (CTKD)進行加密。
CTKD (Cross-Transport Key Derivation) : 在藍芽4.2版本時提出，其目的是為了改善BT和BLE的配對過程，若使用CTKD時，BT與BLE會計算出pairing key並且傳送給彼此，讓對方不用在進行第二次的配對。
#### 如何濫用此機制：
這個key很容易被攻擊者使用Man-in-the-Middle(MitM) 來進行覆寫，攻擊者成功覆寫CTKD後就可以產生出相對應的Long Term Keys(LTK)或者Link Key(LK)來進行非法的連接。

##### 參考資料：
[1] Rasmussen, Kasper. "BLURtooth: Exploiting Cross− Transport Key Derivation in Bluetooth Classic and Bluetooth Low Energy." ASIA Conference on Computer and Communications Security (AsiaCCS). ASIA Conference on Computer and Communications Security (AsiaCCS), 2022.
