# Calculation of Transfer Discounts

According to the 
[Regeln und Definitionen zur Fallabrechnung unter SwissDRG](https://www.swissdrg.org/application/files/3315/2767/3491/SwissDRG_Falldefinitionen_v8.0.pdf)
the transfer discounts ("Verlegungsabschläge") for transferred patients with a
given DRG is calculated like this:

     Transfer discount = daily_transfer_discount * (MLOS - LOS)

where

* `daily_transfer_discount` is defined by the relevant DRG catalogue
* `MLOS` is the mean length of stay for the given DRG -- but see note below!
* `LOS` is the length of stay of the given patient case

**NOTE:** The mean length of stay ("MLOS") per DRG is given in the catalogue on 
the SwissDRG homepage, see e.g. [here](https://www.swissdrg.org/de/akutsomatik/swissdrg-system-70/fallpauschalenkatalog)
for SwissDRG 7.0.  
*But* the MLOS provided there are rounded to one decimal, 
**whereas the grouper uses the MLOS rounded down to the preceding integer!**  

This new way to calculate the transfer discount was introduced with SwissDRG 8.0. 
Section 2.2.3 of the "Bericht zur 
Weiterentwicklung der SwissDRG Tarifstruktur 8.0" describes the changes in detail:

* [deutsch](https://www.swissdrg.org/application/files/6415/3200/6776/Bericht_zur_Entwicklung_der_Tarifstruktur_8.0_Veroeffentlichungsversion.pdf)
* [français](http://www.swissdrg.org/application/files/5115/3200/6813/Bericht_zur_Entwicklung_der_Tarifstruktur_8.0_Veroeffentlichungsversion_f.pdf)
* [italiano](https://www.swissdrg.org/application/files/7915/3200/6837/Bericht_zur_Entwicklung_der_Tarifstruktur_8.0_Veroeffentlichungsversion_i.pdf)

Please note that the application of this updated calculation is mandadory: Grouper
users cannot use the old way of calculation, lest they get invalid effective 
cost weights which differ from the public SwissDRG version.

### Example

Given SwissDRG 8.0 and a transferred patient case with 

* LOS: 5 days
* DRG: A92B. So according to the 
  [catalogue of SwissDRG 8.0](https://www.swissdrg.org/de/akutsomatik/swissdrg-system-80/fallpauschalenkatalog):
  - cost weight: 1.151
  - daily transfer discount:  0.115
  - MLOS: 10.8 *=> grouper uses 10.0*

So the transfer discount is 

    (10 - 5) * 0.115 = 0.575

Therefore the effective cost weight will be 

    1.151 - 0.575 = 0.576  