- Premium features bias possible - advanced search filters??

 
the arguments should be logically valid "and sound".

For one of the argument we want to write it in the extended standard form.


==- Informed Consent - Data Privacy - Mention what data is given to the application==

- Bias of ML Model, culture specific emotion expression

==- Accessibility - Available to the users without smart watches or front cameras.==

Modifying the matchmaking algorithm to accommodate users without smartwatches or other required hardware is a crucial step towards ensuring inclusivity and addressing the accessibility concerns associated with cost and data dependency. By making the application available to a broader range of users, we can mitigate the disadvantage faced by those who are unable to provide biometric data.

One potential modification to the algorithm is to incorporate alternative data sources and user input options. Instead of relying solely on biometric data collected from smartwatches, the algorithm can take into account other relevant information provided by users. This can include preferences, interests, hobbies, or manually entered data such as age, location, and relationship goals.

By expanding the criteria used for matching, the algorithm can offer a more comprehensive and diverse set of potential matches to users who don't have access to smartwatches. This approach ensures that individuals without the required hardware are not disproportionately disadvantaged and still have the opportunity to connect with compatible partners.

Additionally, it is essential to implement a feedback loop within the algorithm to continuously improve its performance and effectiveness. Users without smartwatches can provide feedback on the accuracy and relevance of the matches they receive, which can be used to refine the matching algorithm further.

Furthermore, it is important to communicate clearly to users about the factors influencing the matchmaking process and the alternative options available to those without smartwatches. Transparency and providing users with a choice to participate or opt out of biometric data collection can help build trust and alleviate concerns regarding privacy and exclusion.

By modifying the algorithm to be more inclusive and adaptable to users without smartwatches or other required hardware, the application can foster a more inclusive and accessible matchmaking experience. It allows users from various backgrounds and economic circumstances to participate on an equal footing, reducing frustration and ensuring that everyone has an equal opportunity to find compatible matches.

The matchmaking algorithm should not provide any disadvantage to people who do not have access to smart watches and hardware. 
- Getting less matches causing distress among users

Premises:
Rough conclusion: Algorithm should consider users without biometric data


- Location being publicly available is a problem

- Possibility to be anonymous to the users






- P1: Smartwatch and/or front camera are used to capture biometric data.



- P2: Some users do not have a smartwatch and/or front camera
- P3: if users don't have smartwatch/front camera then we cannot capture their biometric data.
- ==C1: Thus, Biometric data cannot be captured for some users. (P1, P2, P3)==

- P4: Emotion logs are computed using biometric data
- ==C2: Emotion logs cannot be computed for some users (C1, P4)==


- P5: Preference score is computed using emotion logs
- ==C3: Some users will have incorrect preference score. (C2, P5)==

- P6: matchmaking algorithm uses preference score to find matches
- ==C4: some users will get wrong matches (C3, P6)==

- P7: If matchmaking algorithm is modified to be resilient to missing data then, accurate matches can be provided.
- P8: If accurate matches are provided the users of the app will be satisfied.
- ==C5: Thus, ACE should modify the matchmaking algorithm to accommodate users who do not have access to a smartwatch and/or front camera==


