{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "ijGzTHJJUCPY"
   },
   "outputs": [],
   "source": [
    "# Copyright 2023 Google LLC\n",
    "#\n",
    "# Licensed under the Apache License, Version 2.0 (the \"License\");\n",
    "# you may not use this file except in compliance with the License.\n",
    "# You may obtain a copy of the License at\n",
    "#\n",
    "#     https://www.apache.org/licenses/LICENSE-2.0\n",
    "#\n",
    "# Unless required by applicable law or agreed to in writing, software\n",
    "# distributed under the License is distributed on an \"AS IS\" BASIS,\n",
    "# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.\n",
    "# See the License for the specific language governing permissions and\n",
    "# limitations under the License."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "VEqbX8OhE8y9"
   },
   "source": [
    "# Getting Started with the Vertex AI Codey APIs - Code Chat\n",
    "\n",
    "<table align=\"left\">\n",
    "  <td style=\"text-align: center\">\n",
    "    <a href=\"https://colab.research.google.com/github/GoogleCloudPlatform/generative-ai/blob/main/language/code/code_chat.ipynb\">\n",
    "      <img src=\"https://cloud.google.com/ml-engine/images/colab-logo-32px.png\" alt=\"Google Colaboratory logo\"><br> Run in Colab\n",
    "    </a>\n",
    "  </td>\n",
    "  <td style=\"text-align: center\">\n",
    "    <a href=\"https://github.com/GoogleCloudPlatform/generative-ai/blob/main/language/code/code_chat.ipynb\">\n",
    "      <img src=\"https://cloud.google.com/ml-engine/images/github-logo-32px.png\" alt=\"GitHub logo\"><br> View on GitHub\n",
    "    </a>\n",
    "  </td>\n",
    "  <td style=\"text-align: center\">\n",
    "    <a href=\"https://console.cloud.google.com/vertex-ai/workbench/deploy-notebook?download_url=https://raw.githubusercontent.com/GoogleCloudPlatform/generative-ai/blob/main/language/code/code_chat.ipynb\">\n",
    "      <img src=\"https://lh3.googleusercontent.com/UiNooY4LUgW_oTvpsNhPpQzsstV5W8F7rYgxgGBD85cWJoLmrOzhVs_ksK_vgx40SHs7jCqkTkCk=e14-rj-sc0xffffff-h130-w32\" alt=\"Vertex AI logo\"><br> Open in Vertex AI Workbench\n",
    "    </a>\n",
    "  </td>\n",
    "</table>\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "VK1Q5ZYdVL4Y"
   },
   "source": [
    "## Overview\n",
    "\n",
    "This notebook provides an introduction to code chat generation using [Codey chat models](https://cloud.google.com/vertex-ai/docs/generative-ai/code/code-models-overview), specifically the codechat-bison foundation model. Code chat enables developers to interact with a chatbot for assistance with code-related tasks, including debugging, documentation, and learning new concepts. The codechat-bison model is designed to facilitate multi-turn conversations, allowing for a more natural and interactive coding experience.\n",
    "\n",
    "\n",
    "### Vertex AI PaLM API\n",
    "The Vertex AI PaLM API, [released on May 10, 2023](https://cloud.google.com/vertex-ai/docs/generative-ai/release-notes#may_10_2023), is powered by [PaLM 2](https://ai.google/discover/palm2).\n",
    "\n",
    "### Using Vertex AI PaLM API\n",
    "\n",
    "You can interact with the Vertex AI PaLM API using the following methods:\n",
    "\n",
    "* Use the [Generative AI Studio](https://cloud.google.com/generative-ai-studio) for quick testing and command generation.\n",
    "* Use cURL commands in Cloud Shell.\n",
    "* Use the Python SDK in a Jupyter notebook\n",
    "\n",
    "This notebook focuses on using the Python SDK to call the Vertex AI PaLM API. For more information on using Generative AI Studio without writing code, you can explore [Getting Started with the UI instructions](https://github.com/GoogleCloudPlatform/generative-ai/blob/main/language/intro_generative_ai_studio.md)\n",
    "\n",
    "\n",
    "For more information, check out the [documentation on generative AI support for Vertex AI](https://cloud.google.com/vertex-ai/docs/generative-ai/learn/overview)."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "RQT500QqVPIb"
   },
   "source": [
    "### Objectives\n",
    "\n",
    "In this tutorial, you will learn:\n",
    "\n",
    "* Code Debugging\n",
    "* Code Refactoring\n",
    "* Code Review\n",
    "* Code Learning\n",
    "* Code Boilerplates\n",
    "* Prompt Design for Chat\n",
    "  * Chain of Verification\n",
    "  * Self-Consistency\n",
    "  * Tree of Thought\n",
    "  "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "1y6_3dTwV2fI"
   },
   "source": [
    "### Costs\n",
    "This tutorial uses billable components of Google Cloud:\n",
    "\n",
    "* Vertex AI Generative AI Studio\n",
    "\n",
    "Learn about [Vertex AI pricing](https://cloud.google.com/vertex-ai/pricing),\n",
    "and use the [Pricing Calculator](https://cloud.google.com/products/calculator/)\n",
    "to generate a cost estimate based on your projected usage."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "QDU0XJ1xRDlL"
   },
   "source": [
    "## Getting Started"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "N5afkyDMSBW5"
   },
   "source": [
    "### Install Vertex AI SDK"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "kc4WxYmLSBW5",
    "tags": []
   },
   "outputs": [],
   "source": [
    "!pip install google-cloud-aiplatform --upgrade --user"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "j7UyNVSiyQ96"
   },
   "source": [
    "**Colab only:** Uncomment the following cell to restart the kernel or use the button to restart the kernel. For Vertex AI Workbench you can restart the terminal using the button on top."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "YmY9HVVGSBW5"
   },
   "outputs": [],
   "source": [
    "# Automatically restart kernel after installs so that your environment can access the new packages\n",
    "import IPython\n",
    "\n",
    "app = IPython.Application.instance()\n",
    "app.kernel.do_shutdown(True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "6Fom0ZkMSBW6"
   },
   "source": [
    "### Authenticating your notebook environment\n",
    "* If you are using **Colab** to run this notebook, uncomment the cell below and continue.\n",
    "* If you are using **Vertex AI Workbench**, check out the setup instructions [here](https://github.com/GoogleCloudPlatform/generative-ai/tree/main/setup-env)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "LCaCx6PLSBW6"
   },
   "outputs": [],
   "source": [
    "# from google.colab import auth\n",
    "# auth.authenticate_user()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "GckO4EysV5BT"
   },
   "source": [
    "## Code Chat"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "BDYqwDmTLgEy"
   },
   "source": [
    "Vertex AI Codey APIs offer a code chat API geared towards multi-turn conversations tailored for coding scenarios. Leverage the generative AI foundation model, `codechat-bison`, to interface with the code chat API and craft prompts that initiate chatbot-based code dialogues. This guide walks you through the process of creating effective prompts to engage in code-oriented chatbot conversations using the `codechat-bison` model."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "BuQwwRiniVFG"
   },
   "source": [
    "### Import libraries"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Vnq2kIV8yQ97"
   },
   "source": [
    "**Colab only:** Uncomment the following cell to initialize the Vertex AI SDK. For Vertex AI Workbench, you don't need to run this.  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "rtMowvm-yQ97"
   },
   "outputs": [],
   "source": [
    "# import vertexai\n",
    "\n",
    "# PROJECT_ID = \"\"  # @param {type:\"string\"}\n",
    "# vertexai.init(project=PROJECT_ID, location=\"us-central1\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "4zjV4alsiVql"
   },
   "outputs": [],
   "source": [
    "from IPython.display import Markdown, display\n",
    "from vertexai.language_models import CodeChatModel"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "5B1PlCAr3GQi"
   },
   "source": [
    "## Code chat with codechat-bison"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "wzoNuCqd3pbn"
   },
   "source": [
    "The 'codechat-bison' model lets you have a freeform conversation across multiple turns from a code context. The application tracks what was previously said in the conversation. As such, if you expect to use conversations in your application for code generation, use the 'codechat-bison' model because it has been fine-tuned for multi-turn conversation use cases."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "H6AzUJLt-ihw"
   },
   "source": [
    "### Load model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "GqGueQgG3pAA"
   },
   "outputs": [],
   "source": [
    "code_chat_model = CodeChatModel.from_pretrained(\"codechat-bison@002\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "ciT1gFGMd5wK"
   },
   "source": [
    "### Start Chat Session"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "uCHgVidbd8kY"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Rem1FIRcW1lj"
   },
   "source": [
    "### Send Message\n",
    "Once the session is established, you can send prompts, and the model will generate output per the instructions and remember the context."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "bONSaA_SWojK"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"Please help write a function to calculate the min of two numbers in python\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "sqd9ASLWXU3J"
   },
   "source": [
    "You can see that it knows the code generated in the previous step."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "Rlvj2c2HeDgB"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"can you explain the code line by line?\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "CfrIiKuleG_e"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"can you add docstring, typehints and pep8 formating to the code?\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Y3DzEyDYg64l"
   },
   "source": [
    "## Use-cases"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "3REfw-BYg8i2"
   },
   "source": [
    "### Code Debugging"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "jDpwum37X2UE"
   },
   "source": [
    "If you want to minimize variation of the responses, then keep the temperature=0. If you want more samples (outputs), keep greater than 0.2."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "sJ8f2BHMhB9K"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(temperature=0,\n",
    "                                       max_output_tokens=2048)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        '''\n",
    "        Debug the following scenario based on the problem statement, logic, code and error. Suggest possible cause of error and how to fix that.\n",
    "        Expalin the error in detail.\n",
    "\n",
    "        Problem statement: I am trying to write a Python function to implement a simple recommendation system.\n",
    "        The function should take a list of users and a list of items as input and return a list of recommended items for each user.\n",
    "        The recommendations should be based on the user's past ratings of items.\n",
    "\n",
    "        Logic: The function should first create a user-item matrix, where each row represents a user and each column represents an item.\n",
    "        The value of each cell in the matrix represents the user's rating of the item.\n",
    "        The function should then use a recommendation algorithm, such as collaborative filtering or content-based filtering, \\\n",
    "        to generate a list of recommended items for each user.\n",
    "\n",
    "        Code:\n",
    "        ```\n",
    "        import numpy as np\n",
    "\n",
    "        def generate_recommendations(users, items):\n",
    "          \"\"\"Generates a list of recommended items for each user.\n",
    "\n",
    "          Args:\n",
    "            users: A list of users.\n",
    "            items: A list of items.\n",
    "\n",
    "          Returns:\n",
    "            A list of recommended items for each user.\n",
    "          \"\"\"\n",
    "\n",
    "          # Create a user-item matrix.\n",
    "          user_item_matrix = np.zeros((len(users), len(items)))\n",
    "          for user_index, user in enumerate(users):\n",
    "            for item_index, item in enumerate(items):\n",
    "              user_item_matrix[user_index, item_index] = user.get_rating(item)\n",
    "\n",
    "          # Generate recommendations using a recommendation algorithm.\n",
    "          # ...\n",
    "\n",
    "          # Return the list of recommended items for each user.\n",
    "          return recommended_items\n",
    "\n",
    "        # Example usage:\n",
    "        users = [User1(), User2(), User3()]\n",
    "        items = [Item1(), Item2(), Item3()]\n",
    "\n",
    "        recommended_items = generate_recommendations(users, items)\n",
    "\n",
    "        print(recommended_items)\n",
    "        ```\n",
    "        Error:\n",
    "        AttributeError: 'User' object has no attribute 'get_rating'\n",
    "\n",
    "                ```\n",
    "        ''',\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "eDvk7iHzoO0N"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "       can you re-write the function to address the bug of conversion to int inside the function itself?\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "0Yr2U11RB9Cj"
   },
   "source": [
    "### Code Refactoring"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "AoRdCBpMCC9Z"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(max_output_tokens=2048)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Given the following C++ code snippet:\n",
    "        ```c++\n",
    "        class User {\n",
    "        public:\n",
    "          User(const std::string& name, int age)\n",
    "            : name_(name), age_(age) {}\n",
    "\n",
    "          std::string GetName() const { return name_; }\n",
    "          int GetAge() const { return age_; }\n",
    "\n",
    "        private:\n",
    "          std::string name_;\n",
    "          int age_;\n",
    "        };\n",
    "\n",
    "        // This function takes a vector of users and returns a new vector containing only users over the age of 18.\n",
    "        std::vector<User> GetAdultUsers(const std::vector<User>& users) {\n",
    "          std::vector<User> adult_users;\n",
    "          for (const User& user : users) {\n",
    "            if (user.GetAge() >= 18) {\n",
    "              adult_users.push_back(user);\n",
    "            }\n",
    "          }\n",
    "          return adult_users;\n",
    "        }\n",
    "        ```\n",
    "        Refactor this code to make it more efficient and idiomatic.\n",
    "        Make sure to identify and fix potential problems.\n",
    "        Explain the refactoring step by step in detail.\n",
    "        List down potential changes that can be recommended to the user.\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "OPqrxWpyFEni"
   },
   "source": [
    "### Code Review"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "D4yE1XiPMPFC"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(temperature=0,\n",
    "                                       max_output_tokens=2048)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        provide the code review line by line for the following python code: \\n\\n\n",
    "```\n",
    "# Import the requests and json modules\n",
    "import requestz\n",
    "import JSON\n",
    "\n",
    "# Define a class called User\n",
    "class User:\n",
    "    # Define a constructor that takes the user's ID, name, and email as arguments\n",
    "    def __init__(self, id, name, email):\n",
    "        # Set the user's ID\n",
    "        self.userId = id\n",
    "\n",
    "        # Set the user's name\n",
    "        self.userName = name\n",
    "\n",
    "        # Set the user's email\n",
    "        self.userEmail = email\n",
    "\n",
    "    # Define a method called get_posts that gets the user's posts from the API\n",
    "    def getPosts(self):\n",
    "        # Create a URL to the user's posts endpoint\n",
    "        url = \"https://api.example.com/users/{}/posts\".format(self.userId)\n",
    "\n",
    "        # Make a GET request to the URL\n",
    "        response = requestz.get(url)\n",
    "\n",
    "        # Check if the response status code is 200 OK\n",
    "        if response.statusCode != 200:\n",
    "            # Raise an exception if the response status code is not 200 OK\n",
    "            raise Exception(\"Failed to get posts for user {}\".format(self.userId))\n",
    "\n",
    "        # Convert the response content to JSON\n",
    "        posts = JSON.loads(response.content)\n",
    "\n",
    "        # Create a list of Posts\n",
    "        postList = []\n",
    "\n",
    "        # Iterate over the JSON posts and create a Post object for each post\n",
    "        for post in posts:\n",
    "            # Create a new Post object\n",
    "            newPost = Post(post[\"id\"], post[\"title\"], post[\"content\"])\n",
    "\n",
    "            # Add the new Post object to the list of Posts\n",
    "            postList.append(newPost)\n",
    "\n",
    "        # Return the list of Posts\n",
    "        return postList\n",
    "\n",
    "# Define a class called Post\n",
    "class Post:\n",
    "    # Define a constructor that takes the post's ID, title, and content as arguments\n",
    "    def __init__(self, id, title, content):\n",
    "        # Set the post's ID\n",
    "        self.postId = id\n",
    "\n",
    "        # Set the post's title\n",
    "        self.postTitle = title\n",
    "\n",
    "        # Set the post's content\n",
    "        self.postContent = content\n",
    "\n",
    "# Define a main function\n",
    "def main():\n",
    "    # Create a User object for John Doe\n",
    "    user = User(1, \"John Doe\", \"john.doe@example.com\")\n",
    "\n",
    "    # Get the user's posts\n",
    "    posts = user.getPosts()\n",
    "\n",
    "    # Print the title and content of each post\n",
    "    for post in posts:\n",
    "        print(\"Post title: {}\".format(post.postTitle))\n",
    "        print(\"Post content: {}\".format(post.postContent))\n",
    "\n",
    "# Check if the main function is being called directly\n",
    "if __name__ == \"__main__\":\n",
    "    # Call the main function\n",
    "    main()\n",
    "```\n",
    "\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "8vWX03R3PQQz"
   },
   "source": [
    "### Code Learning"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "8HTi-n0KPR6z"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(temperature = 0,\n",
    "                                       max_output_tokens=2048,\n",
    "                                       )\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        '''\n",
    "    I am new to Python and i have not read advanced concepts as of now. can you explain this code line by line.\n",
    "    Include fundamental explanation of some of the advance concepts used in the code as well.\n",
    "    Also provide an explanation as why somebody made a choice of using complex code vs simple code.  \\n\\n\n",
    "\n",
    "    ```\n",
    "    import functools\n",
    "\n",
    "    def memoize(func):\n",
    "      \"\"\"Memoizes a function, caching its results for future calls.\n",
    "\n",
    "      Args:\n",
    "        func: The function to memoize.\n",
    "\n",
    "      Returns:\n",
    "        A memoized version of func.\n",
    "      \"\"\"\n",
    "\n",
    "      cache = {}\n",
    "\n",
    "      @functools.wraps(func)\n",
    "      def memoized_func(*args, **kwargs):\n",
    "        key = tuple(args) + tuple(kwargs.items())\n",
    "        if key in cache:\n",
    "          return cache[key]\n",
    "        else:\n",
    "          result = func(*args, **kwargs)\n",
    "          cache[key] = result\n",
    "          return result\n",
    "\n",
    "      return memoized_func\n",
    "\n",
    "    def lru_cache(maxsize=128):\n",
    "      \"\"\"A least recently used (LRU) cache decorator.\n",
    "\n",
    "      Args:\n",
    "        maxsize: The maximum number of items to keep in the cache.\n",
    "\n",
    "      Returns:\n",
    "        A decorator that wraps a function and caches its results. The least recently\n",
    "        used results are evicted when the cache is full.\n",
    "      \"\"\"\n",
    "\n",
    "      def decorating_function(func):\n",
    "        cache = {}\n",
    "        queue = collections.deque()\n",
    "\n",
    "        @functools.wraps(func)\n",
    "        def wrapper(*args, **kwargs):\n",
    "          key = tuple(args) + tuple(kwargs.items())\n",
    "\n",
    "          if key in cache:\n",
    "            value = cache[key]\n",
    "            queue.remove(key)\n",
    "            queue.appendleft(key)\n",
    "            return value\n",
    "\n",
    "          value = func(*args, **kwargs)\n",
    "          cache[key] = value\n",
    "          queue.appendleft(key)\n",
    "\n",
    "          if len(cache) > maxsize:\n",
    "            key = queue.pop()\n",
    "            del cache[key]\n",
    "\n",
    "          return value\n",
    "\n",
    "        return wrapper\n",
    "\n",
    "      return decorating_function\n",
    "    ```\n",
    "\n",
    "    ''',\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "kb5u10GocF8N"
   },
   "source": [
    "### Code Boilerplates"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "bvLgVo9pcGbd"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(temperature = 0,\n",
    "                                       max_output_tokens=2048)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Write a boilerplate code for FastAPI to serve Llama 7b llm using huggingface locally. Add extra with some boilerplate and #todo for user to fill later:\n",
    "        - input validation steps,\n",
    "        - caching user inputs,\n",
    "        - health check of API,\n",
    "        - database connection with redis server and\n",
    "        - Database connection google cloud SQL,  and\n",
    "        - load balance features\n",
    "\n",
    "        Also, add some test cases that can check the functionality of the Api endpoint with examples.\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Nd2Bu1oWgtrr"
   },
   "source": [
    "## Prompt Design Patterns for Chat"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "kn6nW9Eegx1h"
   },
   "source": [
    "### Chain of Verification\n",
    "\n",
    "Chain of Verification (CoVe) prompting is a technique for refining the code generated by large language models (LLMs) by employing a self-verification process. It aims to mitigate the potential for hallucinations and inaccuracies in the generated code.\n",
    "\n",
    "The CoVe process involves four key steps:\n",
    "\n",
    "1. Drafting an Initial Response: The LLM generates an initial code response based on the provided natural language description.\n",
    "\n",
    "1. Planning Verification Questions: A set of verification questions is formulated to scrutinize the accuracy and completeness of the initial code response.\n",
    "\n",
    "1. Executing Verification: The verification questions are independently answered, either by the LLM itself or by external sources, to minimize potential biases in the verification process.\n",
    "\n",
    "1. Generating a Final Verified Response: Based on the answers to the verification questions, the LLM refines the initial code response to produce a final, more accurate, and reliable code output.\n",
    "\n",
    "CoVe prompting has demonstrated improved performance in code generation tasks compared to traditional prompting methods, resulting in more accurate and reliable code outputs."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "RgZfBIdINYq7"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(max_output_tokens=2048)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        You are a software developer who can take instructions and follow them to generate and modify code.\n",
    "        Your goal is to generate code based on what a user has asked, and to keep modifying the code based on the user's verification rules.\n",
    "        Verification rules are not the same as test functions or test cases.\n",
    "        Instead, they are steps that the user provides to ensure that the code meets their requirements.\n",
    "\n",
    "        For example, if a user asks you to generate a code to calculate the factorial of a number:\n",
    "          Step 1: Initial Setup for function 'calculate_n_factorial'\n",
    "            - Add input:\n",
    "              - n: number\n",
    "            - variables\n",
    "              - temp: store temporary values\n",
    "          As, first step, generate a code to calculate the factorial of a number and setup the function and variables.\n",
    "        and then provides the following verification rules:\n",
    "          Step 2: Verification steps for the factorial function\n",
    "            - The code should return 1 for the input 0.\n",
    "            - The code should return 2 for the input 1.\n",
    "            - The code should return 6 for the input 3.\n",
    "        Now you would modify the code to ensure that it meets the verification rules.\n",
    "\n",
    "        It’s very important to adjust each and every verification in the modification of the code. Each time when the code is modified,\n",
    "        explain your processes. Your job is to self-reflect and correct based on the user input and verification rule.\n",
    "        Do not add anything from your end, just follow user input.\n",
    "        Respond to this context with “Yes, I understand” and do not add any code at this stage. Wait for next instructions.\n",
    "\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "8Ml-jV_iOmYF"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 1:\n",
    "            - Python function ‘calculate_total_cost_parcel’\n",
    "            - Add Input:\n",
    "                - weight\n",
    "                - distance\n",
    "                - shipping_method\n",
    "                - insurance_coverage\n",
    "                - discount_code\n",
    "            - Add Variable:\n",
    "            base_shipping_cost: weight * distance * shipping_method\n",
    "            shipping_method_multiplier = { \"standard\": 1.0, \"expedited\": 1.5, \"overnight\": 2.0 }\n",
    "            insurance_coverage_multiplier =  { \"none\": 1.0, \"basic\": 1.1, \"premium\": 1.2 }\n",
    "            shipping_cost = shipping_method_multiplier[shipping_method]\n",
    "            insurance_cost = insurance_coverage_multiplier[insurance_coverage]\n",
    "            shipping_cost = base_shipping_cost*shipping_cost *insurance_cost\n",
    "            discount = 0\n",
    "            total_cost = shipping_cost-discount [this is what function will return]\n",
    "\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "mWXsH_kEOmu-"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 2: Verification steps for the type hints, docstring, and description for the input to the function:\n",
    "        weight: The weight of the package in kilograms.\n",
    "        distance: The distance the package will be shipped in kilometres.\n",
    "        shipping_method: The shipping method, which can be one of the following:\n",
    "              - “standard\": Standard shipping, which takes 3-5 business days.\n",
    "              - “expedited\": Expedited shipping, which takes 1-2 business days.\n",
    "              -  \"overnight\": Overnight shipping, which takes 1 business day.\n",
    "        insurance_coverage: The insurance coverage, which can be one of the following:\n",
    "              - “none\": No insurance coverage.\n",
    "              -“ basic\": Basic insurance coverage, which covers up to $100 in losses.\n",
    "              - \"premium\": Premium insurance coverage, which covers up to $500 in losses.\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "0pRUhGPTOm_H"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 3: Verification steps for the input to the function:\n",
    "          - Check if the weight is non-negative.\n",
    "          - Check if the distance is non-negative.\n",
    "          - Check if the shipping method is valid.\n",
    "          - Check if the insurance coverage is valid.\n",
    "          - Check if the discount code is valid.\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "FWjd6nLROnPg"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 4: Verification for Discount\n",
    "          - If the discount code is \"SHIP10\", multiply the base shipping cost by 0.10 and subtract the result from the total shipping cost.\n",
    "          - If the discount code is \"SHIP20\", multiply the base shipping cost by 0.20 and subtract the result from the total shipping cost.\n",
    "          - Otherwise, the discount code is invalid, so do not apply any discount.\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "v3l5H125OneG"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 5: Generate test cases that can be used to test the function ‘calculate_total_cost_parcel’. The test cases \\\n",
    "        should include incorrect inputs, unexpected inputs, edge cases that are generally not thought by a developer or a QA.\n",
    "\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "MHrb58x_PtRh"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        How did all the verification steps improve the '‘calculate_total_cost_parcel’' function that was generated?\n",
    "        Explain in details with example, code before and after, and bullet points.\n",
    "        \"\"\",\n",
    "    ).text\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Ku_4Hu20p78v"
   },
   "source": [
    "### Self-Consistency\n",
    "\n",
    "Self-consistency prompting is a technique for enhancing the quality of code generated by large language models (LLMs) by leveraging the model's ability to identify and favor consistent patterns in its reasoning. It aims to address the issue of inconsistent or erroneous code generation by introducing a mechanism for selecting the most consistent and reliable code output among multiple possible options.\n",
    "\n",
    "The self-consistency prompting process involves three key steps:\n",
    "\n",
    "* Generate Multiple Reasoning Paths: The LLM generates multiple distinct reasoning paths, which represent different approaches to solving the given code generation task.\n",
    "\n",
    "* Evaluate Consistency: For each reasoning path, the LLM evaluates the consistency of its intermediate steps and the final code output. This involves identifying patterns, checking for contradictions, and ensuring alignment with the natural language description.\n",
    "\n",
    "* Select the Most Consistent Response: Based on the consistency evaluation, the LLM selects the reasoning path that exhibits the highest level of consistency and produces the most reliable code output.\n",
    "\n",
    "Self-consistency prompting has shown effectiveness in improving the accuracy and reliability of generated code, particularly for complex or ambiguous tasks. It has been demonstrated to reduce the occurrence of inconsistencies and errors, leading to more robust and trustworthy code generation."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "NtaTyPDTp-Uz"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(max_output_tokens=2048)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Input: any english words or group of characters.\n",
    "        Output: reverse of the input string.\n",
    "\n",
    "        Goal:\n",
    "          1) Generate 3 different python code snippets for reverse_string() based on algorithmic complexity and mentioning it along the code.\n",
    "          2) For each code snippet add typehints, docstrings, classes if required, pep8 formatting.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "T2nbVBTQqJWS"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Going forward, i want you to follow each instruction one by one based on the code that is generated in the previous steps:\n",
    "\n",
    "        Step 1:  For each code snippet, generate a test case that checks if the function reverses the string correctly. The test cases \\\n",
    "        should include incorrect inputs, unexpected inputs, edge cases that are generally not thought by a developer or a QA.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "cm9gmE8oqgaD"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 2: For each code snippet, Intergate the exception handling for incorrect inputs, unexpected inputs, \\\n",
    "        edge cases that based on previous step and re-write the functions\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "x2ntdIF-9h63"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 3 : Based on the test written, code completeness and algorithm complexity, select the code which is best\n",
    "\n",
    "        Step 4:  Explain the reasoning in detail as bullet points of why this is selected compared to other options.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "wdruvyiV9iU6"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Step 5: Show the code which is selected along with its test cases.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "R06g2qKRJT5F"
   },
   "source": [
    "### Tree of Thought\n",
    "\n",
    "Tree of Thought (ToT) prompting is a technique for guiding large language models (LLMs) to generate code by breaking down the task into a hierarchical structure of intermediate natural language steps. This approach aims to address the limitations of traditional prompting methods, which can lead to LLM getting stuck in local optima or generating code that is not well-structured or optimized.\n",
    "\n",
    "The ToT prompting process involves three key steps:\n",
    "\n",
    "* Decomposing the Task: The natural language description of the task is broken down into a series of smaller subtasks, forming a tree-like structure.\n",
    "\n",
    "* Generating Intermediate Thoughts: For each subtask in the tree, the LLM generates a corresponding intermediate thought, which is a natural language explanation of how to solve that subtask.\n",
    "\n",
    "* Constructing the Code: The LLM combines the intermediate thoughts into a cohesive and structured code output, following the hierarchical organization of the tree.\n",
    "\n",
    "ToT prompting has demonstrated advantages over traditional prompting methods in code generation tasks, particularly for complex or multi-step problems. It helps the LLM to reason about the problem in a more structured and systematic way, leading to more efficient and reliable code generation."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "KMkNoHZJJTQw"
   },
   "outputs": [],
   "source": [
    "code_chat = code_chat_model.start_chat(max_output_tokens=2048,\n",
    "                                       temperature=0.5)\n",
    "\n",
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Imagine a tree of thoughts, where each thought represents a different step in the data preprocessing pipeline.\n",
    "        The goal of this pipeline is to run a regression model on a ecommerce data from bigquery.\n",
    "        Start at the root of the tree, and write down a thought that captures the main goal of the data preprocessing pipeline.\n",
    "        Then, branch out from that thought and write down two more thoughts that represent related steps in the pipeline.\n",
    "        Continue this process until you have a complete tree of thoughts, with each leaf representing a single line of Python code.\n",
    "        For each branch and leaf, only write the thoughts and not code. Do not write code for each branch and leaves and put them in proper markdown.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "AW96iuyuLYfb"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        The data also needs to be joined across different tables in BigQuery before starting pre-processing.\n",
    "        For example customer table has to be merged with the order table.\n",
    "        this should be added at the initial branchess a thought.\n",
    "        After that Add more branches for model building using BQML once the data is scaled.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "vvOvmMQZJjKS"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Reconfigure the branches from the root as per the newly added thoughts. Follow the proper flow. rewrite the whole branches and leaves\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "XhjjkABRJ-st"
   },
   "outputs": [],
   "source": [
    "print(code_chat.send_message(\n",
    "        \"\"\"\n",
    "        Generate the code for each branch and leaves.\n",
    "        \"\"\",\n",
    "    )\n",
    ")"
   ]
  }
 ],
 "metadata": {
  "colab": {
   "provenance": [],
   "toc_visible": true
  },
  "environment": {
   "kernel": "python3",
   "name": "tf2-gpu.2-11.m108",
   "type": "gcloud",
   "uri": "gcr.io/deeplearning-platform-release/tf2-gpu.2-11:m108"
  },
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.10.10"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
