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

## Files Tree

```text
├── code
│   ├── main.py
│   ├── step1_process_document.py
│   ├── step2_test_skill.py
│   ├── step3_detect_problem.py
│   ├── model
│   │   └── ...
│   ├── skill
│   │   └── ...
│   └── util
│       └── ...
├── config
│   └── config000.ini
├── corpus
│   └── ...
├── dataset
│   ├── benchmark_stable.xlsx
│   └── large_scale.xlsx
├── README.md
└── requirement.txt
```

* code: contains the source code of Elevate
* config: the configuration file.
* dataset: Small-Complex-Dataset and Large-Stable-Dataset
* requirement.txt: requirements of python packages

## Requirement

### packages
`pip install -r requirement.txt`

`python -m spacy download en_core_web_sm`

### environment

To use the Azure OpenAI GPT-4-Turbo:
1. Execute: `export OPENAI_API_KEY=<YOUR OPENAI API KEY>`
2. Change the configuration file **config000.ini** in the config directory, replace apibase and apiversion based on the configuration in your own deployment.

To use DeepSeek-R1:
1. Execute: `export DEEPSEEK_API_KEY=<YOUR DEEPSEEK API KEY>`

### other
1. The **chromedriver** that matches your chrome version
2. An **amazon developer account** that can use the simulator (change the config000.ini in the config directory to add your acount)
3. an **Azure account** that has access to the openAI API, or a **DeepSeek account** that has access to the DeepSeek API.
4. We need to login to the amazon developer account to start using the simulator. In most of the cases amazon will send an email to your linked email address for verifications. We use pop to read the emails, so make sure the **110 port** is open.

## How to run Elevate

```
cd code

python main.py 
    -i <begin index of skill, default as 1> 
    -ei <end index of skill, default as 4001> 
    -c <configuration file name in the config directory, default as config000.ini> 
    -e <input skills file in the dataset directory, default as large_scale.xlsx> 
    -l <path to save communication logs and results, default as ../../output/large_scale> 
    -m <input the LLM to implement Elevate, possible selections are GPT4 and DeepSeek, default as DeepSeek>
```