# How LLMs work?

#### Imagine a Student Named “Obama”

He wanted to be a language expert. Obama doesn’t know anything about human language at first.

***

{% stepper %}
{% step %}
### **Training: Feeding** Obama **the World’s Knowledge**

Obama sat down in a giant library. The library has:

* Books
* Websites
* News
* Social media
* Code
* Scientific papers

**Billions and billions of words.** For months, Obama **reads nonstop**.&#x20;

{% hint style="success" %}
But Obama isn’t memorizing; instead, Obama is **learning patterns**
{% endhint %}

| How do words usually follow each other? | “I like to drink _coffee_ in the morning.”              |
| --------------------------------------- | ------------------------------------------------------- |
| What words often appear together?       | “Artificial Intelligence” or “peanut butter and jelly.” |
| How does a sentence start and end?      | "Once upon a time...", "In conclusion,..."              |
{% endstep %}

{% step %}
### **How Obama Learns: The Transformer Brain**

Now, Obama’s brain is made of something special called a **Transformer model**.

The transformer has a superpower called **Attention**.

* When Obama reads a sentence, Obama doesn’t just go word-by-word.
* Instead, **Obama looks at all the words at once** and figures out which words are important to each other.

Example:

> _"The cat sat on the mat because it was warm."_

Obama knows that **“it”** refers to **“the mat,”** not “the cat”, because of the context.

{% hint style="success" %}
This **attention power** is what makes LLMs so smart compared to older models.
{% endhint %}
{% endstep %}

{% step %}
### **Breaking Language into Tokens**

Obama doesn’t read letter by letter. Instead, Obama breaks sentences into **tokens**, like puzzle pieces. Tokens can be:

* A word
* A part of a word
* Sometimes a single letter or punctuation mark

**Example:**

> _"Unbelievable"_ → might be tokenized as: “Un”, “believe”, “able”

{% hint style="warning" %}
Why? Because that helps Obama understand how words are built and reused.
{% endhint %}
{% endstep %}

{% step %}
### **Predicting the Next Token**

Now, during training, Obama plays one game again and again: **“Guess the next token!”**

> If the sentence is: _"I went to the..."_

Obama tries to guess:

* **store?**
* **market?**
* **park?**

It tries many times, gets corrected by its teachers (mathematics called “**loss function**”), and **improves every day**.

After billions of guesses, Obama becomes very good at knowing **what words should come next**.
{% endstep %}

{% step %}
### **Using the LLM: Generating Text**

Now Obama is ready. He is now a language expert. You give it a prompt:

> _"Tell me a story about a dragon."_

Obama thinks:

* Based on everything he had read
* Based on how stories usually go
* What kind of tone should he use?

Then Obama **predicts**&#x20;

* **The first token** → “Once”
* Then the second → “upon”&#x20;
* Then the third → “a”&#x20;
* Then the fourth → “time”

And so on… until it’s written the entire story!
{% endstep %}
{% endstepper %}
