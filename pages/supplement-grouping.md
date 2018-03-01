## 1.1.0
With the release of Java Grouper 1.1.0 we extend the grouping functionality to also be capable of
calculating appropriate supplements for patient cases. Therefor, we had to introduce some changes
to the grouper API.

We have also updated the Batchgrouper so it is able to group supplements (see below).

**Note:**

> The SwissDRG [Online-Grouper](https://grouper.swissdrg.org/) is not able yet to group supplements, since supplement 
grouping is currently being beta-tested. This goes for both the online single case and batch grouper.  
>
> Supplement grouping with the Online-Grouper will be available on 30.11.2018.

### Supplement Grouping using the Batchgrouper

To use the batchgrouper from the 1.1.0 branch to group supplements based on a BFS ("MedStat") file, issue this command 
on the command line:

```
java -jar java-grouper-1.1.0-BETA-all.jar -cat path/to/catalogue-acute.csv -f bfs -out results.csv path/to/workspace.json medstat-patientdata.dat
``` 
(see [description of the batchgrouper](https://swissdrg.github.io/grouper-documentation/pages/batchgrouping.html) for 
details)

This command outputs a normal DRG-grouper result file (here named "results.csv") and a second output file with the
supplement grouper results, named "results.csv.ze", at the same place.

Instead of a BFS patient file, you can also use the new 
[batchgrouper format 2017](https://swissdrg.github.io/grouper-documentation/pages/format-batchgrouper-2017.html) (since 
it also contains the medication data).

To see the batchgrouper's options, call it without any parameters:

    java -jar path/to/java-grouper-1.1.0-BETA-all.jar 

     
### Supplement Grouping using the GrouperKernel
#### Changes

##### New public API package
You can now use all classes and interfaces in the `org.swissdrg.zegrouper.api` top level package. Note that only classes and interfaces
from this package are projected to be stable across releases.

##### PatientCase
Instances of the `PatientCase` class now offer methods to add medications. This change was necessary to facilitate
the calculation of supplements based on ATC codes.

#### Supplement Grouping
The SwissDRG Java Grouper can now calculate applicable supplements for patient cases. For the batchgrouper, full supplement grouping is only
available with the 'batch_2017' and 'bfs' input formats, since these are the only formats which provide medication information.

##### Loading a supplement grouper
The `IGrouperReader` interface was extended and now provides the `loadSupplementGrouper(String)` method, which
returns an instance of `ISupplementGrouper`. 

##### Calculating supplements
In order to calculate supplements of a `PatientCase`, you first have to decorate it by creating an instance of 
`SupplementPatientCase`. You can then pass this instance to the `ISupplementGrouper#group` method, which returns
an `ISupplementGroupResult`. This result holds all calculated supplements, along with additional information.

##### Example

```java

import java.io.IOException;
import org.swissdrg.grouper.*;
import org.swissdrg.zegrouper.api.*;

class SupplementExample {
    public static void main(String[] args) throws Exception, IOException {
        // Setup patient case
        PatientCase pc = new PatientCase();
        pc.setId("test");
        pc.setAgeYears(30);
        pc.setPdx(new MainDiagnosis("B26.9"));
        pc.addProcedure("39.95.21");
        pc.addMedication("L01XC06::IV:600:mg");

        // Load Groupers
        IGrouperReader reader = new SpecificationReader();

        IGrouperKernel drgGrouper = reader.loadGrouper("path/to/spec/with/supplements", IGrouperKernel.Tariff.SWISSDRG);
        ISupplementGrouper supplementGrouper = reader.loadSupplementGrouper("path/to/spec/with/supplements");

        // Group into DRG
        drgGrouper.groupByReference(pc);

        // Calculate Supplements
        SupplementPatientCase sPc = new SupplementPatientCase(pc);
        
        ISupplementGroupResult result = supplementGrouper.group(sPc);

        // Access calculated supplements
        System.out.println(result);
    }
}

```

#### Potentially breaking changes
The following list states changes that potentially break applications built against the older 1.0.X branch. Please note that 
we only state potentially breaking changes to classes within the public API packages.

* `PatientCase#toString` now returns a string representation in Batchgrouper 2017 format instead of the previous Batchgrouper format. 
If you have to use that particular format, you should use the `PatientCase#toLegacyString` method.
* Due to the addition of medications, `PatientCase#urlEncode` now returns an extended URL compatible representation of a patient case.
* `PatientCaseParserFactory.getParserFor` method with format `PatientCaseParserFactory.InputFormat.URL` will now return 
a Parser capable of parsing the extended URL format as explained above.
