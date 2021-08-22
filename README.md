# GSoC2021

![ViewCount](https://views.whatilearened.today/views/github/kaushl2208/GSoC2021.svg)
![GitHub](https://img.shields.io/github/followers/kaushl2208?style=social)
![Twitter](https://img.shields.io/twitter/follow/Kaushl2208?style=social)
![GitHub Stars](https://img.shields.io/github/stars/Kaushl2208/GSoC2021?style=social)

<a href = "https://summerofcode.withgoogle.com/projects/?sp-search=Kaush#4988663051583488">  ![Summer-of-code](/files/GSoC_header.png)</a>


<div align = "center"><h2><i>Copyright False Positive Detection using ML @ <a href = "https://www.fossology.org/">FOSSology </a> </i></h2></div>

<p align="center">
	<a href="#project-details">Project Details</a> | 
	<a href="#contributions">Contributions</a> | 
	<a href="#deliverables">Deliverables</a> | 
	<a href="#future-goals">Future Goals</a> | 
	<a href="#key-takeaways">Key Takeaways</a>
</p>


<h1 align = "center" id = "project-details">Project Details</h1>

In the current scenario, most of the projects use copyright agents defining the mode of usage for
their product. Software like Fossology uses the rule-based approaches for copyright detection
and scanning. Agents like nomos use Regex based approaches to extract the statements from a
project then a Regex based agent shows the results with the several copyrights statements used in
the project followed by a set of agents which does the Deactivation of the copyrights which are False. Still, there are a lot of statements that are left in the Agent's findings and then an user has to involve in the Manual findings. It has become a two step process which was not ideal for the case.

My proposed ideas and objectives revolved around Fossology entirely i.e. from including a Natural Language Processing based approach for pre-processing and then recognising a pattern between the false and a true copyright statement with the help of NLP and Automation. Another given functionality was to remove the clutter from original extracted copyright statement. Entire goal for the proposed ideas was to introduce new functionalities into Fossology.

<br>

<h1 align = "center" id = "contributions"> Contributions</h1>
<h2>1. Introducing NER and POS tagging for Copyright Statements <img src="https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/thumbs/160/joypixels/257/scroll_1f4dc.png" width="25"></h2>
<p><i>A Python based approach to analyse copyright statements</i></p>

- Codebase: [GitHub](https://github.com/Kaushl2208/FalsePositiveDetection)
- Documentation: [FalsePositiveDetection-repo](https://github.com/Kaushl2208/FalsePositiveDetection/blob/master/README.md)

One thing about copyright statements is very intriguing i.e. They looks so predictable but there are millions variations to how they look and how many things they can contain. Despite of having a predictable architecture. The first task revolved around from understanding the architecture with the close filter of all types of (TEXT UNDERSTANDINGS) i.e. the types of entities and the parts of speech in our case. 

From there, I decided to predict a specific structure that is being followed by most of the copyright statements despite the variations to how they look. Two different lists of Named Entities and POS tags are then hypothised. These hypothised lists helped in benchmarking as an ideal structure. It cleared the further understanding and outine of the complete project.

According to NER, the structure looked like: <br>

Statement: "Copyright (c) 2021, Kaushlendra Pratap (kaushlendra@xyz.com)" <br>
Probable NER Entity looks Like: ['DATE', 'PERSON', 'CARDINAL', 'ORG'] <br>
Probable POS Tags looks Like: ['NOUN', 'NUM', 'PROPN', 'PROPN'] <br>

<h2>2. Hypothesis to Working Solution <img src="https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/thumbs/160/google/298/hammer-and-pick_2692-fe0f.png" width="35"></h2>

After testing the architecture predicted and getting good accuracy in recognising most of the copyright statements from the required datasets provided. The compilation of the script started. The working of script looked like:
<br>
![WorkFlow-Diagram](/files/MyDiagram1Final.png)
<br>

The task was divided into three sections:
- Text-preprocessing to make the input data more accurate and with less clutter.
- Defining a function that calculated the NER and POS tag for each statement, iteratively.
- Two stage filtering if-else ladder with the mechanism to update "T" if the match is found and "F" if not, A new characteristic was introduced in CSV i.e."is_copyright"


<h2>3. Testing the script and Accuracy Calculations <img src="https://media.giphy.com/media/CTfg7SZKO10Pe/giphy.gif" width="25"></h2>

**Accuracy Calculation :**

The accuracy calculation was done with the help of several datasets which are manually marked by Human. Iterating over the CSV,

`IF ManualTag == AlgorithmTag:`
    `counter += 1;`
`accuracy_score = (counter/total_occurence)*100`

The accuracy was divided into: FP_accuracy, TP_accuracy, TN_accuracy and FN_aacuracy.

Final Precision = `(TP + FP)/(TP + FP + TN + FN)`

<h3> Results <h3>
<br>

![AccuracyScore](/files/FinalAccuracy.png)

Final Precision: 94.37 %

<br>

<h2>4. Clutter Removal from the Copyright Statements</h2> 
<img src="https://www.charityfootprints.com/images/kab/sweeper.gif">

Copyright statements are not ideally with direct structure that comprise of license statements appended to them at the end.

Normal Copyright with clutter:

`Copyright (c) 2021, Kaushlendra Pratap Singh. Distributed Under the MIT license ....`

Copyright with clutter removal:

`Copyright (c) 2021, Kaushlendra Pratap Singh`

The approach taken was:

`IF is_copyright == "t"`
`string = copyrightStatement;`
`IF 'ORG', 'PERSON' in NER_LIST:`
` clutter = string[0:string.index(org_name)] (same way person_name)`


**RESULTS :**

![Cluter-Removal](/files/ClutterRemovalPicture.png)

<h2>5. Integrating the Script as Decider Agent 
<img src="https://www.tatacommunications.com/apps/survey/assets/survey/images/3/5.gif" width = "100"></h2>

Fossology has a list of several agents like Nomos, Monk, Ninka, Decider etc. etc. The main goal was to intoroduce the python script into the PHP code and then use it as a Decider agent.

The tasks in hand were: 

1. To create two flags on UI with *Copyright Deactivation* and *Copyright Deactivation with Clutter removing*
2. Create two rules and two seperate function in `DeciderAgent.php` to call the python script and then Update the *Database* with the True and Deactivated copyright statements.
3. To differentiate between the functionality of both the functions and providing the absolute `$uploadID, $content, $action and $hash`.
4. Installing changes in the Makefile to install the script with `make install`.
5. Creating a `mod_deps` file to introduce and install the dependencies required to run the script.

Each task was accomplished and the agent was completely integrated.

**RESULTS**

Last two entries:
<br>
![DeciderAgent](/files/DeciderAgent.png)

<b>The after working results :</b>

No Clutter Removal Flag:
<br>
![WithoutClutterDeactivation](/files/FinalWorkingNocluter.png)

<b>Clutter Removal Flag:</b>
<br>
![WithClutterRemovalDeacitvation](/files/FINALPICTURS.png)


<h2>6. Documentation and Pull Request </h2>

1. The Pull request with the script, integration changes, UI change and Database updation code is: [Check the PR from here](https://github.com/fossology/fossology/pull/2083)

2. The Progress has been regularly marked every week and they are kept in a seperate wiki. [Check WPRs from here](https://fossology.github.io/gsoc/docs/2021/copyrights/)

3. The setting up and user documentation of the script in Decider Agent can be visited here: [Check documentation](https://fossology.github.io/gsoc/docs/2021/copyrights/setup)

4. The installation and user documentation for the Jupyter Notebook can be visited here: [README](https://github.com/Kaushl2208/FalsePositiveDetection/blob/master/README.md)


<h1 align="center" id = "deliverables"> Deliverables <img src="https://media1.tenor.com/images/7d87e607145c7156f2e5321d1751e813/tenor.gif" width="60"></h1>

| Tasks   | Planned | Completed     | Remarks    |
| :---:       |    :----:   |    :---:      |    :---:      |
| Introducing NER and POS tagging for Copyright Statements     | Yes       | :heavy_check_mark: | This was like the POC for the idea. |
| Implementing the Hypothesis as a working product.    | Yes       | :heavy_check_mark: | The working of the script is efficient but can be improved further. |
| Accuracy Score calculation and Testing   | Yes       | :heavy_check_mark: | The accuracy is acceptable but can be improved with more checks involved  |
| Integrating the Script with Fossology   | Yes       | :heavy_check_mark: | Integration is done and can be used with fossology installation  |
| Documenting the working of Script  | Yes       | :heavy_check_mark: | NONE  |

<br>

<h1 align="center" id = "future-goals"> Future Goals  <img src="https://thumbs.gfycat.com/LongWateryAmericansaddlebred-max-1mb.gif" width="40"></h1>

1. Implementing further more layer of checks to cover the edge cases.
2. Going through other NLP techniques to understand some other perspectives of the copyright statements.
3. Maintaining the agent and look for achieving further more accuracy in clutter removal techniques.
4. Be with Fossology community as contributor and help future developers to get started with Fossology, Atarashi and Nirjas.
5. Continue Maintaining Atarashi and Nirjas.



<br> 
<h1 align="center" id = "key-takeaways"> Key Takeaways <img src="https://www.netanimations.net/livres-13.gif" width="60"></h1>

- Learnt the art of collaboration and working on real-time software development.
- Improved programming skills, including OOP concepts and Modular Programming.
- Learnt alot about NLP Techniques for pre-processing texts.
- Learnt about importance of Open-Source Copyrights and their detail figurative analysis.
- Improved Git skills.
- How a full fledge system like fossology functions in Model, View and Controllers perspectives.
- Better analysis of code and debugging more easily.
- Importance of a well equipped dataset and creating one from scratch for training our own NER model.
- Punctuality and adaptability according to time and situation.
- Communicating properly, presenting the code and keep on asking doubts.

<h1 align="center" id = "Acknowledgements"> Acknowledgements</h1>

This year Google Summer of Code came with extra fun because it was my second time participating with Fossology and It is going with a little sadness because it was my last time to be participating with fossology as student developer. There are several people to whom I want to extend my regards to.

I want to thank and appreciate my mentors [Michael C. Jaeger](https://www.linkedin.com/in/michael-c-jaeger-b896922/), [Anupam Ghosh](https://www.linkedin.com/in/anupam-ghosh-053bab4/), [Gaurav Mishra](https://www.linkedin.com/in/gmishx/), [Vasudev Maduri](https://www.linkedin.com/in/vasudevmaduri/), [Ayush Bharadwaj](https://www.linkedin.com/in/hastagab/) and [Shaheem Azmal M MD.](https://www.linkedin.com/in/shaheem-azmal-m-md-71604429/) without the help and support from them, all this would not have been possible.

Now, I would like to extend my regards to two very important figures who helped me to steer across all the challenges(PS: Not just GSoC :P), [Ayush Bharadwaj](https://www.linkedin.com/in/hastagab/) and [Sahil Jha](https://www.linkedin.com/in/sjha2048/).

Finally, I am glad to meet all the fellow developers down the road. You guys are awesome and keep doing the great work.

# Reach out to me
- [Connect with me on LinkedIn](https://www.linkedin.com/in/hastagab/)
- [Give a follow on GitHub](https://github.com/kaushl2208) 
- [Follow me at Twitter](https://twitter.com/Kaushl2208)
- [Email](kaushlendrapratap.9837@gmail.com)

[![](https://img.shields.io/badge/Made%20With%20❤️%20By-Kaushlendra-red)](https://github.com/Kaushl2208)
