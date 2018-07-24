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
You'll find detailled description in section 2.2.3 of the "Bericht zur 
Weiterentwicklung der SwissDRG Tarifstruktur 8.0":

* [deutsch](https://www.swissdrg.org/application/files/6415/3200/6776/Bericht_zur_Entwicklung_der_Tarifstruktur_8.0_Veroeffentlichungsversion.pdf)
* [français](http://www.swissdrg.org/application/files/5115/3200/6813/Bericht_zur_Entwicklung_der_Tarifstruktur_8.0_Veroeffentlichungsversion_f.pdf)
* [italiano](https://www.swissdrg.org/application/files/7915/3200/6837/Bericht_zur_Entwicklung_der_Tarifstruktur_8.0_Veroeffentlichungsversion_i.pdf)

Please note that the application of this updated calculation is mandadory: Grouper
users cannot use the old way of calculation, lest they get invalid effective 
cost weights which differ from the public SwissDRG version.

### Example

Given SwissDRG 7.0 and a transferred patient case with 

* LOS: 10 days
* DRG: A42A. So according to the [catalogue of SwissDRG 7.0](https://www.swissdrg.org/de/akutsomatik/swissdrg-system-70/fallpauschalenkatalog):
  - cost weight: 4.823
  - daily transfer discount:  0.22
  - MLOS: 20.8 *=> grouper uses 20.0*

So the transfer discount is 

    (20 - 10) * 0.22 = 2.2

Therefore the effective cost weight will be 

    4.823 - 2.2 = 2.447  

as illustrated
[here](https://manual70.swissdrg.org/drgs/5a1d5e5ab09492ce9bfd58ed?utf8=%E2%9C%93&los%5B%5D=10&adm_mode_filter=00&sep_mode_filter=06).