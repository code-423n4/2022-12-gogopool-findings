lack of input sanitation can cause waste of gas for node operators 

protocol documentation have stated acceptable input values like 14 - 365 days duration, when creating a minipool values like 7 days or 366 days are not checked and are accepted. Even though there is a manual review by rialto before initiateStaking, this will be a waste of gas for node operators who created the minipool with bad parameters as they have to resubmit another call instead of reverting at the first bad parameter call. 

