---
icon: floppy-disk
---

# Review 161 en 1 AES

\
![](../.gitbook/assets/DSC01180.JPG) ![](../.gitbook/assets/DSC01181.JPG)

![](../.gitbook/assets/DSC01183.JPG) ![](../.gitbook/assets/DSC01190.JPG)<br>

<figure><img src="../.gitbook/assets/DSC01189.JPG" alt=""><figcaption></figcaption></figure>

| Attribute           | Value                                                  |
| ------------------- | ------------------------------------------------------ |
| Product type        | Arcade multi-game cartridge                            |
| Architecture        | Dual-board system (Board A + Board B)                  |
| Total weight        | **358 g**                                              |
| Included components | 2 PCB boards + plastic shell + printed label           |
| Enclosure           | Injection-molded plastic cartridge housing             |
| Label               | Printed adhesive sticker (front artwork)               |
| Power domain        | 3.3 V / 5 V mixed logic system                         |
| Interconnect        | 8-pin inter-board connector (J2 / J3A depending board) |

#### BOM – 161-in-1 (Board A)

<figure><img src="../.gitbook/assets/DSC01157.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/DSC01178.JPG" alt=""><figcaption></figcaption></figure>

<div><figure><img src="../.gitbook/assets/DSC01161 (1).JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01162.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01163.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01164.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01165.JPG" alt=""><figcaption></figcaption></figure></div>

<table><thead><tr><th>RefDes</th><th>Component</th><th width="140">Type</th><th>Package</th><th>Part Number / Value</th><th>Function</th><th>Notes</th></tr></thead><tbody><tr><td>U2</td><td>Altera MAX 3000A CPLD</td><td>Programmable Logic</td><td>TQFP-144</td><td>EPM3256ATC144-10</td><td>Main bus decoding and control logic</td><td>Handles memory banking and ROM address decoding</td></tr><tr><td>U3</td><td>Altera MAX II CPLD</td><td>Programmable Logic</td><td>TQFP-100</td><td>EPM240T100C5N</td><td>Auxiliary system logic</td><td>Likely menu control or signal arbitration</td></tr><tr><td>S1</td><td>Parallel NOR Flash</td><td>Memory</td><td>TSOP-56</td><td>JS28F256M29EWH (256 Mbit / 32 MB)</td><td>ROM storage</td><td>Part of shared ROM pool (P/C/V mapping depending on design)</td></tr><tr><td>M1</td><td>Parallel NOR Flash</td><td>Memory</td><td>TSOP-56</td><td>JS28F512M29EWL (512 Mbit / 64 MB)</td><td>Main ROM storage</td><td>Likely program ROM or mixed code/data storage</td></tr><tr><td>C1 / C2</td><td>F0095H0</td><td>High-density NOR Flash module</td><td>BGA / custom module</td><td>~8 Gbit (~1 GB estimated total)</td><td>Main ROM backend storage</td><td>Internally composed of multiple Micron-style dies</td></tr><tr><td>CE1 / CE2</td><td>SMD aluminum electrolytic capacitor</td><td>Passive component</td><td>SMD radial</td><td>100 µF / 10 V</td><td>Power supply filtering</td><td>Stabilizes 3.3V / 5V rails under load</td></tr><tr><td>U1</td><td>Voltage regulator (LDO)</td><td>Power management</td><td>SOT-223</td><td>1117 3.3 BWQ61</td><td>3.3V regulation</td><td>Main logic supply rail</td></tr><tr><td>J2</td><td>Connector</td><td>Board connector</td><td>Through-hole</td><td>8-pin connector</td><td>Inter-board communication</td><td>Link between Board A and Board B</td></tr><tr><td>C1, C2, C4, C5, C6, C7, C13, C16</td><td>SMD capacitors</td><td>Passive component</td><td>SMD</td><td>Unspecified</td><td>Decoupling / filtering</td><td>Local power stabilization and noise suppression</td></tr></tbody></table>

#### BOM – 161-in-1 (Board B)

<figure><img src="../.gitbook/assets/DSC01168.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/DSC01171.JPG" alt=""><figcaption></figcaption></figure>

<div><figure><img src="../.gitbook/assets/DSC01166.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01167.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01173.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01174.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01175.JPG" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/DSC01177.JPG" alt=""><figcaption></figcaption></figure></div>



<table><thead><tr><th width="114">RefDes</th><th>Component</th><th width="138">Type</th><th>Package</th><th>Part Number / Value</th><th>Function</th><th>Notes</th></tr></thead><tbody><tr><td>P1</td><td>High-density NOR Flash</td><td>Memory</td><td>BGA / custom module</td><td>F0095H0</td><td>ROM storage (program/data segment)</td><td>Part of main game data backend</td></tr><tr><td>P2</td><td>High-density NOR Flash</td><td>Memory</td><td>BGA / custom module</td><td>F0095H0</td><td>ROM storage (program/data segment)</td><td>Paired with P1 for expanded storage</td></tr><tr><td>U5</td><td>74LS245</td><td>Bus transceiver</td><td>SOIC / DIP variant</td><td>12AYHEK LS245</td><td>Data bus buffering</td><td>Directional bus isolation</td></tr><tr><td>U6</td><td>74LS245</td><td>Bus transceiver</td><td>SOIC / DIP variant</td><td>91CKYGK LS245</td><td>Data bus buffering</td><td>Secondary bus driver</td></tr><tr><td>U8</td><td>STC Microcontroller</td><td>MCU</td><td>LQFP-44</td><td>STC89C52RC</td><td>System control MCU</td><td>Menu/system logic controller</td></tr><tr><td>X1</td><td>Crystal / Oscillator</td><td>Clock source</td><td>HC-49 / SMD</td><td>FS11.05P</td><td>System clock generation</td><td>Frequency reference for MCU/CPLD</td></tr><tr><td>CP1</td><td>Altera MAX 3000A CPLD</td><td>Programmable logic</td><td>TQFP-144</td><td>EPM3256ATC144-10N</td><td>Logic control / banking</td><td>Core address decoding logic</td></tr><tr><td>PCM2</td><td>Altera MAX 3000A CPLD</td><td>Programmable logic</td><td>TQFP-144</td><td>EPM3256ATC144-10N</td><td>Secondary logic control</td><td>Likely ROM mapping / signal control</td></tr><tr><td>U7</td><td>Voltage regulator</td><td>LDO</td><td>SOT-223</td><td>1117 3.3 BWQ61</td><td>3.3V power regulation</td><td>Main logic rail supply</td></tr><tr><td>D1</td><td>Diode</td><td>Protection diode</td><td>SMD</td><td>T4</td><td>Reverse polarity / protection</td><td>Power input protection</td></tr><tr><td>PC1–PC5, PC22–PC23</td><td>SMD capacitors</td><td>Passive</td><td>SMD</td><td>Unspecified</td><td>Decoupling / filtering</td><td>Local power stabilization</td></tr><tr><td>C9, C10</td><td>SMD capacitors</td><td>Passive</td><td>SMD</td><td>Unspecified</td><td>Decoupling / filtering</td><td>Noise suppression near logic</td></tr><tr><td>R1–R9</td><td>SMD resistors</td><td>Passive</td><td>SMD</td><td>103 (10 kΩ)</td><td>Pull-up / pull-down network</td><td>Bus stabilization / logic bias</td></tr><tr><td>J3A</td><td>Connector</td><td>Board connector</td><td>Through-hole / edge</td><td>8-pin connector</td><td>Inter-board communication</td><td>Link between Board A and B</td></tr></tbody></table>



#### 🕹️ Lista de juegos

> Eliminados hacks, bootlegs, conversiones no oficiales, versiones modificadas y títulos duplicados.
>
> Resultado: **92 juegos únicos** del catálogo NeoGeo MVS.

***

## 🥊 Juegos de Lucha

<table><thead><tr><th width="74">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>1</td><td>🥊 SNK vs Capcom: SVC Chaos</td></tr><tr><td>2</td><td>👑 The King of Fighters '94</td></tr><tr><td>3</td><td>👑 The King of Fighters '95</td></tr><tr><td>4</td><td>👑 The King of Fighters '96</td></tr><tr><td>5</td><td>👑 The King of Fighters '97</td></tr><tr><td>6</td><td>👑 The King of Fighters '98</td></tr><tr><td>7</td><td>👑 The King of Fighters '99</td></tr><tr><td>8</td><td>👑 The King of Fighters 2001</td></tr><tr><td>9</td><td>👑 The King of Fighters 2002</td></tr><tr><td>10</td><td>👑 The King of Fighters 2003</td></tr><tr><td>11</td><td>⚔️ Samurai Shodown</td></tr><tr><td>12</td><td>⚔️ Samurai Shodown II</td></tr><tr><td>13</td><td>⚔️ Samurai Shodown III</td></tr><tr><td>14</td><td>⚔️ Samurai Shodown IV</td></tr><tr><td>15</td><td>⚔️ Samurai Shodown V Special</td></tr><tr><td>16</td><td>🌙 The Last Blade</td></tr><tr><td>17</td><td>🌙 The Last Blade 2</td></tr><tr><td>18</td><td>🐺 Garou: Mark of the Wolves</td></tr><tr><td>19</td><td>🐉 Double Dragon</td></tr><tr><td>20</td><td>🥋 Art of Fighting 2</td></tr><tr><td>21</td><td>💥 Karnov's Revenge</td></tr><tr><td>22</td><td>🧢 Fatal Fury</td></tr><tr><td>23</td><td>🌎 World Heroes</td></tr><tr><td>24</td><td>🌎 World Heroes 2</td></tr><tr><td>25</td><td>🔥 Breakers</td></tr><tr><td>26</td><td>🔥 Breakers Revenge</td></tr><tr><td>27</td><td>⚡ Savage Reign</td></tr><tr><td>28</td><td>🤝 Kizuna Encounter</td></tr><tr><td>29</td><td>🐲 Rage of the Dragons</td></tr></tbody></table>

***

## 🚀 Shoot'em Up

<table><thead><tr><th width="68">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>30</td><td>✈️ Strikers 1945</td></tr><tr><td>31</td><td>✈️ Aero Fighters 2</td></tr><tr><td>32</td><td>✈️ Aero Fighters 3</td></tr><tr><td>33</td><td>⭐ Blazing Star</td></tr><tr><td>34</td><td>🌌 Pulstar</td></tr><tr><td>35</td><td>🚀 Alpha Mission II</td></tr><tr><td>36</td><td>🛰️ Last Resort</td></tr><tr><td>37</td><td>👁️ Viewpoint</td></tr><tr><td>38</td><td>⚔️ Zed Blade</td></tr><tr><td>39</td><td>🚁 Andro Dunos</td></tr><tr><td>40</td><td>🍅 Captain Tomaday</td></tr><tr><td>41</td><td>🦕 Prehistoric Isle 2</td></tr></tbody></table>

***

## 💣 Acción / Run & Gun

<table><thead><tr><th width="71">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>42</td><td>💥 Metal Slug</td></tr><tr><td>43</td><td>💥 Metal Slug 2</td></tr><tr><td>44</td><td>💥 Metal Slug X</td></tr><tr><td>45</td><td>💥 Metal Slug 3</td></tr><tr><td>46</td><td>💥 Metal Slug 4</td></tr><tr><td>47</td><td>🔫 Shock Troopers 2nd Squad</td></tr><tr><td>48</td><td>🥷 Ninja Commando</td></tr><tr><td>49</td><td>🏹 Top Hunter</td></tr><tr><td>50</td><td>🔵 Blue's Journey</td></tr><tr><td>51</td><td>🤖 Robo Army</td></tr><tr><td>52</td><td>☢️ Mutation Nation</td></tr><tr><td>53</td><td>🏯 Sengoku</td></tr><tr><td>54</td><td>🏯 Sengoku 3</td></tr><tr><td>55</td><td>🤖 Eight Man</td></tr><tr><td>56</td><td>👊 Burning Fight</td></tr><tr><td>57</td><td>⚔️ Crossed Swords</td></tr></tbody></table>

***

## ⚽ Deportes

<table><thead><tr><th width="66">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>58</td><td>⚽ Super Sidekicks 2</td></tr><tr><td>59</td><td>⚽ Super Sidekicks 3</td></tr><tr><td>60</td><td>⚽ Ultimate 11</td></tr><tr><td>61</td><td>🏆 Neo Geo Cup '98</td></tr><tr><td>62</td><td>⚽ Pleasure Goal</td></tr><tr><td>63</td><td>🤖 Soccer Brawl</td></tr><tr><td>64</td><td>🏈 Football Frenzy</td></tr><tr><td>65</td><td>🏀 Street Hoop</td></tr><tr><td>66</td><td>⚾ Baseball Stars 2</td></tr><tr><td>67</td><td>⚾ 2020 Super Baseball</td></tr><tr><td>68</td><td>🐎 Stakes Winner</td></tr><tr><td>69</td><td>🐎 Stakes Winner 2</td></tr><tr><td>70</td><td>⛳ Neo Turf Masters</td></tr></tbody></table>

***

## 🧩 Puzles y Casual

<table><thead><tr><th width="67">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>71</td><td>🫧 Puzzle Bobble</td></tr><tr><td>72</td><td>🫧 Puzzle Bobble 2</td></tr><tr><td>73</td><td>🎈 Puzzle de Pon</td></tr><tr><td>74</td><td>🎈 Puzzle de Pon! R</td></tr><tr><td>75</td><td>✨ Magical Drop 2</td></tr><tr><td>76</td><td>✨ Magical Drop III</td></tr><tr><td>77</td><td>💣 Panic Bomber</td></tr><tr><td>78</td><td>💰 Money Puzzle Exchanger</td></tr></tbody></table>

***

## 🏎️ Carreras

<table><thead><tr><th width="72">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>79</td><td>🏁 Over Top</td></tr><tr><td>80</td><td>🚗 Thrash Rally</td></tr><tr><td>81</td><td>🚙 Neo Drift Out</td></tr></tbody></table>

***

## 🎮 Otros

<table><thead><tr><th width="67">Nº</th><th width="300">Juego</th></tr></thead><tbody><tr><td>82</td><td>😂 Matrimelee</td></tr><tr><td>83</td><td>🏴‍☠️ Spin Master</td></tr><tr><td>84</td><td>⚔️ Ganryu</td></tr><tr><td>85</td><td>🎱 Bang Bead</td></tr><tr><td>86</td><td>🥏 Battle Flip Shot</td></tr><tr><td>87</td><td>🎭 Far East of Eden: Kabuki Klash</td></tr><tr><td>88</td><td>🥊 Legend of Success Joe</td></tr><tr><td>89</td><td>👹 King of Monsters</td></tr><tr><td>90</td><td>♟️ Master of Syougi</td></tr><tr><td>91</td><td>🐰 Zupapa</td></tr></tbody></table>

***

## 📊 Estadísticas

<table><thead><tr><th width="393">Categoría</th><th width="112" align="right">Cantidad</th></tr></thead><tbody><tr><td>📦 Juegos anunciados en el cartucho</td><td align="right">161</td></tr><tr><td>🚫 Hacks / bootlegs / conversiones</td><td align="right">~69</td></tr><tr><td>🎮 Juegos únicos reales</td><td align="right">~92</td></tr><tr><td>🏢 Juegos oficiales NeoGeo</td><td align="right">~85</td></tr><tr><td>⭐ Imprescindibles del catálogo</td><td align="right">~65</td></tr></tbody></table>

***

## 📝 Conclusión

Aunque el cartucho se comercializa como **"161-in-1"**, tras eliminar:

* 🚫 Hacks
* 🚫 Bootlegs
* 🚫 Conversiones no oficiales
* 🚫 Versiones Plus/Magic/Orochi
* 🚫 Duplicados

el contenido real queda reducido a unos **92 títulos distintos**, de los cuales aproximadamente **85 son juegos oficiales del catálogo NeoGeo MVS**.

Esto significa que cerca del **43% del menú está compuesto por variantes  añadidas para inflar la cifra de juegos anunciados, si lo llamaran 92 en 1, seria mas realista.**

<figure><img src="../.gitbook/assets/DSC01191.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/DSC01192.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/DSC01194.JPG" alt=""><figcaption></figcaption></figure>

\
Enlace de compra, cupon descuento `SRZLYFLHFDMW`

{% embed url="https://es.aliexpress.com/item/1005007640720198.html" %}
