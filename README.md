# CBT439
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 439 is from Volker Mielke of Bremen, Germany and          *   FILE 439
//*           contains his PDSX utility to scan all partitioned     *   FILE 439
//*           datasets in an installation for the presence of a     *   FILE 439
//*           given member name.                                    *   FILE 439
//*                                                                 *   FILE 439
//*           This file is the source library for PDSX.             *   FILE 439
//*                                                                 *   FILE 439
//*             Volker Mielke                                       *   FILE 439
//*             St. - Gallener - Str. 17                            *   FILE 439
//*             28325 Bremen                                        *   FILE 439
//*             GERMANY                                             *   FILE 439
//*                                                                 *   FILE 439
//*             Phone: +49 421 4099152                              *   FILE 439
//*             Email:  vmielke@debitel.net                         *   FILE 439
//*                                                                 *   FILE 439
//*  ----------------------------------------------------------     *   FILE 439
//*                                                                 *   FILE 439
//*     PDSX - PARTITIONED DATASET MEMBER CROSS-REFERENCE           *   FILE 439
//*                                                                 *   FILE 439
//*         "Volker Mielke" <vmielke@debitel.net>                   *   FILE 439
//*                                                                 *   FILE 439
//*     1. WHAT IS PDSX?                                            *   FILE 439
//*           WITH PDSX YOU CAN FIND OUT WHICH DATASET(S)           *   FILE 439
//*           ON YOUR SYSTEM CONTAIN A GIVEN MEMBER.                *   FILE 439
//*           UNDER TSO YOU SIMPLY ENTER                            *   FILE 439
//*              PDSX <MEMBERNAME>                                  *   FILE 439
//*           AND YOU WILL BE SHOWN A LIST OF ALL                   *   FILE 439
//*           APPLICABLE DATASETS, FROM WHERE YOU CAN               *   FILE 439
//*           DIRECTLY EDIT OR BROWSE THE REQUESTED                 *   FILE 439
//*           MEMBER(S).                                            *   FILE 439
//*                                                                 *   FILE 439
//*     2. HOW DOES IT WORK?                                        *   FILE 439
//*           EVERY NIGHT WE RUN A VTOC SCAN ON ALL OUR             *   FILE 439
//*           DISK PACKS (WE DO THIS ANYWAY FOR RECOVERY            *   FILE 439
//*           PURPOSES, SO THERE IS NO EXTRA OVERHEAD). WE          *   FILE 439
//*           USE THE VTOC PROGRAM FROM THE CBT TAPE FOR            *   FILE 439
//*           THIS.                                                 *   FILE 439
//*                                                                 *   FILE 439
//*           THEN WE FILTER THE VTOC OUTPUT TO CREATE A            *   FILE 439
//*           LIST OF ALL DESIRED PO / PDSE - DATASETS.             *   FILE 439
//*                                                                 *   FILE 439
//*           THE DIRECTORIES OF THESE DATASETS ARE READ,           *   FILE 439
//*           THE RESULT IS SORTED AND LOADED INTO A VSAM           *   FILE 439
//*           KSDS.  THE DIRECTORY SCAN TAKES ABOUT 15 MIN          *   FILE 439
//*           ELAPSED TIME AND ABOUT 1.5 CPU MINUTES FOR            *   FILE 439
//*           ABOUT 4,500 DATASETS AND 600,000 MEMBERS.             *   FILE 439
//*                                                                 *   FILE 439
//*     3. INSTALLATION                                             *   FILE 439
//*                                                                 *   FILE 439
//*           COPY THE LOAD MODULES PDSMEM1 AND PDSMEM4 TO          *   FILE 439
//*           A LOAD LIBRARY OR RECOMPILE THEM.                     *   FILE 439
//*                                                                 *   FILE 439
//*        3.1 THE BATCH PART                                       *   FILE 439
//*            MODIFY THE SAMPLE JOB IN MEMBER $BATCH TO            *   FILE 439
//*            MEET YOUR STANDARDS.                                 *   FILE 439
//*                                                                 *   FILE 439
//*            MODIFY THE VTOCFLTR EXEC TO EXCLUDE FILE NOT         *   FILE 439
//*            WANTED IN YOUR XREF (I.E. ISPF PROFILES,             *   FILE 439
//*            CONFIDENTIAL FILES...)  PLAN TO RUN THE JOB          *   FILE 439
//*            ON A REGULAR BASIS.                                  *   FILE 439
//*                                                                 *   FILE 439
//*        3.2 ONLINE                                               *   FILE 439
//*            COPY THE PANELS(PDSMEM2A,PDSMEM2B,PDSHLP2A)          *   FILE 439
//*            TO A PANEL LIBRARY.                                  *   FILE 439
//*                                                                 *   FILE 439
//*            COPY THE REXX EXEC "PDSX" TO YOUR                    *   FILE 439
//*            SYSPROC/SYSEXEC FILE AND MODIFY THE LIBDEF           *   FILE 439
//*            STATEMENT TO POINT TO YOUR PANEL LIBRARY.            *   FILE 439
//*                                                                 *   FILE 439
//*     4. KNOWN PROBLEMS                                           *   FILE 439
//*        PO DATASETS ARE OPENED EVERY NIGHT BY PDSX TO            *   FILE 439
//*        SCAN THEIR DIRECTORIES. THIS CAUSES THEIR LAST           *   FILE 439
//*        REFERENCE DATE TO BE CHANGED.                            *   FILE 439
//*                                                                 *   FILE 439
//*        IF YOU USE HSM, HSM WILL NEVER CONSIDER THESE            *   FILE 439
//*        DATASETS FOR MIGRATION, BECAUSE THEY SEEM TO             *   FILE 439
//*        HAVE BEEN ACCESSED RECENTLY.  FOR US THIS IS             *   FILE 439
//*        NOT A PROBLEM, BECAUSE WE DO NOT INDEX USER              *   FILE 439
//*        LIBRARIES, AND PRODUCTION/SYSTEM LIBRARIES ARE           *   FILE 439
//*        NOT CONSIDERED MIGRATION CANDIDATES BY OUR               *   FILE 439
//*        SHOP.                                                    *   FILE 439
//*                                                                 *   FILE 439
//*        POSSIBLE SOLUTIONS COULD BE                              *   FILE 439
//*           - TO DO A "QUIET" OPEN ON THE DATASET, I              *   FILE 439
//*             KNOW SOME DISK MANAGEMENT TOOLS OPEN FILE           *   FILE 439
//*             WITHOUT CHANGING THE LAST REFERENCE DATE,           *   FILE 439
//*                                                                 *   FILE 439
//*             OR                                                  *   FILE 439
//*           - LOOK AT THE LAST REFERENCE DATE FOR A               *   FILE 439
//*             DATASET BEFORE READING ITS DIRECTORY. IF            *   FILE 439
//*             IT HAS NOT BEEN CHANGED, THE DIRECTORY              *   FILE 439
//*             INFORMATION COLLECTED DURING THE PREVIOUS           *   FILE 439
//*             RUN CAN SIMPLY BE COPIED FROM THE EXISTING          *   FILE 439
//*             XREF - DATASET.  BUT THIS WOULD REQUIRE A           *   FILE 439
//*             LITTLE BIT OF LOGIC TO BE ADDED                     *   FILE 439
//*                                                                 *   FILE 439
//*        SOMETIMES I HEAR PEOPLE COMPLAIN THAT THEY DON'T         *   FILE 439
//*        SEE A MEMBER THAT THEY KNOW IT EXISTS. THIS IS           *   FILE 439
//*        BECAUSE YOU CANNOT EXPECT PDSX TO KNOW ABOUT             *   FILE 439
//*        MEMBERS CREATED AFTER IT HAS BEEN RUN.  THE SAME         *   FILE 439
//*        IS TRUE FOR DELETED MEMBERS.                             *   FILE 439
//*                                                                 *   FILE 439
//*     5. ENHANCEMENTS                                             *   FILE 439
//*        WHAT I WOULD LIKE TO DO (IF I HAD THE TIME) :            *   FILE 439
//*                                                                 *   FILE 439
//*        - FIX THE HSM PROBLEM ABOVE                              *   FILE 439
//*        - ADD RACF SUPPORT TO SHOW ONLY DATASETS                 *   FILE 439
//*          ACCESSIBLE TO A USER                                   *   FILE 439
//*        - SWITCH FROM VTOC SCANS TO DCOLLECT. THIS               *   FILE 439
//*          WOULD MAKE IT POSSIBLE TO KEEP DIRECTORY               *   FILE 439
//*          INFORMATION FOR MIGRATED AND / OR BACKED UP            *   FILE 439
//*          DATASETS.                                              *   FILE 439
//*        - ABILITY TO DO A PARTIAL INDEX REFRESH (FOR             *   FILE 439
//*          SELECTED DATASETS OR VOLUMES)                          *   FILE 439
//*                                                                 *   FILE 439
//*     6. COPYRIGHT                                                *   FILE 439
//*                                                                 *   FILE 439
//*        COPYRIGHT 1990,1999 BY VOLKER MIELKE                     *   FILE 439
//*                            VOLKER MIELKE EDV - BERATUNG         *   FILE 439
//*        ALL RIGHTS RESERVED                                      *   FILE 439
//*                                                                 *   FILE 439
//*        YOU MAY USE, REDISTRIBUTE AND MODIFY THIS                *   FILE 439
//*        PROGRAM, BUT IT MUST NOT BE SOLD.                        *   FILE 439
//*                                                                 *   FILE 439
//*        USE OF THIS PROGRAM IS AT YOUR OWN RISK.                 *   FILE 439
//*                                                                 *   FILE 439
```
