# Grouping

You can use the JavaGrouper from within your own software.

The relevant classes of the Grouper API are all in the base package `org.swissdrg.grouper` 
and in the package `org.swissdrg.grouper.tarpsy`. 

***IMPORTANT:***  Only classes contained within these two packages are guaranteed to provide a 
stable interface during minor updates.

All other classes might change it the future!

## Grouping SwissDRG (Akutsomatik) patients

Here is a simple example of how to use the grouper API to group a SwissDRG patient case 
("Akutsomatik"):
 
```java
import org.swissdrg.grouper.Catalogue;
import org.swissdrg.grouper.IGrouperKernel.Tariff;
import org.swissdrg.grouper.EffectiveCostWeight;
import org.swissdrg.grouper.GrouperResult;
import org.swissdrg.grouper.IGrouperKernel;
import org.swissdrg.grouper.IGrouperReader;
import org.swissdrg.grouper.IPatientCaseParser;
import org.swissdrg.grouper.PatientCase;
import org.swissdrg.grouper.PatientCaseParserFactory;
import org.swissdrg.grouper.PatientCaseParserFactory.InputFormat;
import org.swissdrg.grouper.SpecificationReader;
import org.swissdrg.grouper.WeightingRelation;

import java.util.Map;

public class ApiExample {

    // specs as published by SwissDRG on download.swissdrg.org
    private static final String WORKSPACE_FOLDER = "/path/to/spec-6av";
    private static final String CATALOGUE_FILE   = "/path/to/spec-6av/catalogue-acute.csv";

    public static void main(String[] args) throws Exception {

        // First, create a patient by parsing a string in Batchgrouper input format
        
        // "BATCH" is the input parser format; alternatives: "URL" for a UrlPatientCaseParser, "BFS" for a BFSPatientCaseParser 
        IPatientCaseParser parser = PatientCaseParserFactory.getParserFor(InputFormat.BATCH, Tariff.SWISSDRG);

        // Input string in 
        // Batchgrouper input format as described in https://docs.swissdrg.org/grouper-doku-de.pdf
        String inputLine = "123456;75;0;;W;01;00;9;0;;M179;M0694;I1090;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;815421:L:20151026;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;";

        PatientCase patient = parser.parse(inputLine);

        // Load grouper
        IGrouperReader reader = new SpecificationReader();
        IGrouperKernel grouper = reader.loadGrouper(WORKSPACE_FOLDER, Tariff.SWISSDRG);

        // see java-grouper-1.0.10-javadoc/org/swissdrg/grouper/IGrouperKernel.html#groupByReference-org.swissdrg.grouper.PatientCase-
        grouper.groupByReference(patient);

        // Retrieve grouper result and its parts (pcg, cost weight)
        GrouperResult result = patient.getGrouperResult();

        Map<String, WeightingRelation> catalogue = Catalogue.createFrom(CATALOGUE_FILE);

        System.out.println("DRG: " + result.getDrg());
        EffectiveCostWeight ecw = EffectiveCostWeight.calculateEffectiveCostWeight(patient, catalogue.get(result.getDrg()));
        System.out.println("ECW: " + ecw.getEffectiveCostWeight());

        /** should print:
         *
         *    DRG: I43B
         *	  ECW: 19650
         *
         */
    }
}
```

## Grouping TARPSY patients

And here is a simple example of how to use the grouper API and TARPSY package to group a TARPSY patient: 

```java
import org.swissdrg.grouper.*;
import org.swissdrg.grouper.IGrouperKernel.Tariff;
import org.swissdrg.grouper.PatientCaseParserFactory.InputFormat;
import org.swissdrg.grouper.tarpsy.TarpsyCatalogue;
import org.swissdrg.grouper.tarpsy.TarpsyEffectiveCostWeight;
import org.swissdrg.grouper.tarpsy.TarpsyWeightingRelation;

import java.util.Map;

public class GrouperDemoTarpsy {

    // specs as published by SwissDRG on download.swissdrg.org
    private static final String WORKSPACE_FOLDER      = "/path/to/specs/t1.2";
    private static final String TARPSY_CATALOGUE_FILE = "/path/to/specs/t1.2/catalogue.csv";

    public static void main(String[] args) throws Exception {

        // First, create a patient by parsing a string in Batchgrouper input format
        
        // "BATCH" is the input parser format; alternatives: "URL" for a UrlPatientCaseParser, "BFS" for a BFSPatientCaseParser
        IPatientCaseParser parser = PatientCaseParserFactory.getParserFor(InputFormat.BATCH, Tariff.SWISSDRG);

        // Input string in Batchgrouper input format as described in https://docs.swissdrg.org/grouper-doku-de.pdf
        String patientInputLine = "123456;75;0;;W;01;00;9;0;;F199;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;94A114::20170101;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;20170101;20170109";

        PatientCase patient = parser.parse(patientInputLine);

        // Load Grouper
        IGrouperReader reader = new SpecificationReader();
        IGrouperKernel grouper = reader.loadGrouper(WORKSPACE_FOLDER, Tariff.TARPSY);

        // Group into DRG
        // see java-grouper-1.0.10-javadoc/org/swissdrg/grouper/IGrouperKernel.html#groupByReference-org.swissdrg.grouper.PatientCase-
        grouper.groupByReference(patient);

        // Retrieve grouper result and its parts (pcg, cost weight)
        GrouperResult result = patient.getGrouperResult();

        Map<String, TarpsyWeightingRelation> catalogue = TarpsyCatalogue.createFrom(TARPSY_CATALOGUE_FILE);

        String pcg = result.getDrg();
        System.out.println("PCG: " + pcg);
        TarpsyEffectiveCostWeight ecw = TarpsyEffectiveCostWeight.calculateTarpsyEffectiveCostWeight(
                patient, catalogue.get(pcg));
        System.out.println("ECW: " + ecw.getEffectiveCostWeight());
        System.out.println("Daily cost weight: " + ecw.getDayCostWeight());

        /** should print:
         *
         *    PCG: TP21A
         *	  ECW: 9783
         *	  Daily cost weight: 1087
         *
         */
    }
}
```
