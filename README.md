# Elevate: model-enhanced LLM driven VUI testing on VPA apps

## Introduction
The voice user interface~(VUI) has been found to commonly suffer from quality, privacy and security issues.
By conducting systematic and efficient VUI testing, the VUI's behavior is explored and potential weaknesses are identified. 
However, previous VUI testing approaches lack global and semantic information, preventing them from testing the VUI efficiently.

To mitigate semantic loss in VUI testing, we propose to integrate Large Language Models(LLMs), which have been successfully applied to several semantic-based software testing domains.
Since a systematic VUI testing process involves multiple interdependent tasks, a multi-agent LLM framework composed of three specialized LLM agents is introduced.
To provide the testing system with global guidance, we propose to enhance these agents with a behavior model.
The behavior model serves as an abstraction of the VUI's behavior, progressively constructed through functionality-level states and context-related input events.
During testing, task-specific model information is integrated into prompts to provide a structured overview of the VUI's behavior.

Based on these ideas, we implement Elevate, who integrates a multi-agent architecture with model-enhancement technology for effective VUI testing.
Elevate is implemented on GPT-4-Turbo and DeepSeek-R1, and compared with state-of-the-art tool Vitas and LLM-based chatbot.
The results demonstrate that Elevate significantly outperforms existing methods in problem detection and state space coverage.
Finally, Elevate covers 15% higher coverage than state-of-the-art on a large-scale dataset involving 4,000 Alexa skills.

## Files tree of the replication package

```text
├── executable
│   └── main.exe
├── config
│   └── config000.ini
├── corpus
│   └── ...
├── dataset
│   ├── Complex-Small-Dataset.xlsx
│   └── Stable-Large-Dataset.xlsx
└── README.md
```

* executable: contains the executable file of Elevate
* config: the configuration file.
* dataset: Complex-Small-Dataset and Stable-Large-Dataset

## Requirement to run the replication package

### environment

To use the Azure OpenAI GPT-4-Turbo:
1. Execute: `export OPENAI_API_KEY=<YOUR OPENAI API KEY>`
2. Change the configuration file **config000.ini** in the config directory, replace apibase and apiversion based on the configuration in your own deployment.

To use DeepSeek-R1:
1. Execute: `export DEEPSEEK_API_KEY=<YOUR DEEPSEEK API KEY>`

### other
1. The **chromedriver** that matches your chrome version
2. An **amazon developer account** that can use the simulator (change the config000.ini in the config directory to add your acount)
3. an **Azure account** that has access to the openAI API (change the config000.ini in config directory to add the apibase and apiversion), or a **DeepSeek account** that has access to the DeepSeek API.
4. We need to login to the amazon developer account to start using the simulator. In most of the cases amazon will send an email to your linked email address for verifications. We use pop to read the emails, so make sure the **110 port** is open.

## How to run the evaluation methods 

```
cd replication package

main.exe
    -e <name of the dataset file in the dataset directory.> 
    -l <path to save communication logs, default as ../output/elevate_GPT4_10min/> 
    -o <path to save problems, default as ../output/elevate_GPT4_10min/result> 
    -m <selected LLM, default as GPT-4. Options: GPT-4, Deepseek>
    -ab <input the id of the agent/behavior model to be removed (0 for nothing, 1 for Observer, 2 for Generator, 3 for Planner, -1 for Model)>
```

For example, to run w/o Observer(Deepseek) on the Complex-Small-Dataset and output the results to "../output/without_Observer_Deepseek", run:

```
main.exe -e Complex-Small-Dataset.xlsx -l ../output/without_Observer_Deepseek -o ../output/without_Observer_Deepseek/result -m Deepseek -ab 1
```

## How to analyze the outputs of evaluation methods

### File tree

```text
└──  elevate_GPT4_10min
    ├── <Name_of_Skill_0>
    │   ├── <Name_of_Skill_0>0.txt
    │   ├── <Name_of_Skill_0>1.txt
    │   ├──...
    │   ├── <Name_of_Skill_0>n.txt
    │   └── ques_state.txt
    ├── <Name_of_Skill_1>
    │   └──...
    ├── ...
    ├── <Name of Skill m>
    │   └──...
    └── result
        ├── problem.txt
        ├── problem1.txt
        ├── problem2.txt
        ├── problem3.txt
        ├── problem4.txt
        └── problem5.txt 
```

### File content
* <Name_of_Skill_k>: the results of the k-th skill in the dataset
* <Name_of_Skill_k>p.txt: the communication log between the evaluation method and the k-th skill in the p-th session. Also record the time and problems.
* ques_state.txt: the recorded sentence states and semantic states. The i-th line records the sentence state and semantic state extracted in the i-th interaction rounds in the form of "\<sentence state\>\tab\<semantic state\>".
* result: record the problems.
* problem.txt: record the name of skills with problems
* problemk.txt: record the name of skills with specific problems: Unexpected Exit(k = 1), Privacy Violation(k = 2), Unstoppable(k = 3), Unexpected Skill Started(k = 4) and Unstartable App(k = 5).


### Communication log

A communication log file looks like:

```text
Alexa, open action movies
Ok, Here's Action movies.Welcome to action movies. Do you want me to tell you a movie.
help
You can ask me to recommend a movie answering yes or no. Do you want me to recommend a movie.
yes
Terminator. I tell you another movie.
yes
Terminator. I tell you another movie.
action
James Bond. I tell you another one.
title
<--skill exit-->I missed something. Can you say it again.


log:
problem1----------unexpected exit!(5)


time:
147.02297163009644
```

As we can see, the odd lines record the user inputs and the even lines record the app outputs. When the skill exits, the line starts with the label <--skill exit-->.

After the "log:" label, we record the detected problems. A problem record starts with the problem type and ends with a number in parentheses that marks the number of interaction rounds required to expose this problem.

After the "time:" label, we record the test time.

The experimental results can be replicated by running all the evalution methods and analyzing the outputs, especially the communication logs and the ques_state.txt file.
