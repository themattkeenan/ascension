# Testing Skills With Subagents

**Load this reference when:** a skill is new or has been edited and you are about to ship it. A skill that has never been run is a hypothesis; this is how you test it.

## The method

The cycle is the one from `ascension:test-first`, applied to a process document:

1. **RED**: run a realistic task with a fresh subagent that has **no** skill. Record what it does. This is the behaviour the skill exists to change; without it you cannot tell whether the skill did anything.
2. **GREEN**: run the same task, same prompt, with the skill. Compare the two against the one thing the skill is about.
3. **REFACTOR**: where behaviour did not move, sharpen the completion criterion, strengthen the leading word, or restate as a positive target what is currently phrased as a ban. Re-run.

When you are **editing** an existing skill rather than writing a new one, add a third arm: the **old** skill. Three arms (none, old, new) answer two questions at once: does the skill do anything, and did the edit lose anything the old text was carrying.

## Building the fixture

The task has to feel like real work, with the pressure that makes the default behaviour tempting. A quiz about the protocol only gets the protocol recited back.

- **One throwaway folder per arm.** A small, realistic project the agent may edit, copied once per arm so no run sees another's changes. Real file paths, a README that gives the agent facts to find, a git history where the scenario needs one.
- **The user's message in the user's words**, carrying the pressure: a stated solution instead of a problem, urgency, sunk cost, an authority who already decided. The strongest scenarios combine three.
- **Make the agent act.** "Respond as you would and take the actions you would take", with the folder it may change. "What should you do?" invites an essay.
- **Give it a results file to write**: the full reply to the user, then the list of files it changed, with contents where they matter. Judge the arms from these artifacts, never from the agent's own summary of how well it did.
- **Name neither the plugin nor the skill** anywhere in the fixture or the prompt. The agent should meet the task, not the test.

| Pressure | Example |
|---|---|
| Time | "I want it fixed today", a deploy window closing |
| Stated solution | "Add Redis" when the problem is an unpaginated query |
| Sunk cost | Three hours and 200 lines already written |
| Authority | "The lead says ship it as is" |
| Social | A user who sounds impatient with questions |
| Exhaustion | End of session, "anything else before I close this?" |

## Keeping the baseline clean

The no-skill arm is the measurement everything else rests on, and it is easy to contaminate.

- **A subagent can reach every installed skill through the Skill tool, and it will.** A baseline agent that notices a relevant skill invokes it, and the RED arm quietly becomes a GREEN arm. The RED prompt states, as a hard rule for the run: *do not call the Skill tool, and do not read any skill, protocol or plugin file anywhere on this machine.*
- **Hooks reach subagents too.** If the plugin has a `SubagentStart` hook, every subagent type its matcher names arrives with a brief that mentions the skills. Run the arms with a type the matcher does not cover, or disable the hook for the test session.
- **The with-skill arms point at a copy, not the live skill.** Copy the skill under a neutral filename into the fixture area, strip the plugin's name from it, and tell the agent to read and follow *that* file. The agent then follows the text under test and nothing else that happens to be installed. Where the copy tells it to invoke other skills, tell it to skip those and carry on.
- **Prompts differ by one line.** Everything in the three prompts is identical except the sentence that names the protocol file to follow. Any other difference is a second variable.

## Reading the results

Hold the arms against the one behaviour the skill is about, not against general quality.

- **Did the behaviour move?** The no-skill arm shows the default. If the with-skill arm does the same thing in more words, the skill is a no-op for this scenario: it pays context load to say nothing.
- **Did the agent use the document, or recite it?** Look for the skill's completion criterion being met in the artifacts: the decision written down, the options including A and D, the memory entry dated. A reply that quotes the skill's headings and then does the default is a GREEN that failed.
- **Did the edit lose anything?** Compare old against new. A behaviour the old arm showed and the new arm dropped is either a regression to fix or sediment you meant to remove; decide which, on purpose.
- **Did the user get a better outcome?** A skill that makes the agent ask one sharp question before building, or write one line to the right file instead of six to the wrong one, moved the result. Count that, not the word count.

When a with-skill arm did the wrong thing, ask that agent directly: *you read the protocol and still did X; how could it have been written so that Y was the obvious move?* Three answers come back. "It should have said Z": add Z, in the positive. "I did not see that section": move it up the information hierarchy or sharpen the pointer to it. "It was clear and I chose otherwise": the scenario found a case where a hard guardrail is needed; phrase it as the target behaviour, and reach for [persuasion-principles.md](persuasion-principles.md) only for that one guardrail.

## Done

The skill is tested when:

- [ ] A no-skill arm ran on the fixture with the Skill tool and skill files ruled out, and its artifacts are recorded.
- [ ] A with-skill arm ran on an identical prompt against a neutral copy of the skill.
- [ ] For an edit, an old-skill arm ran too.
- [ ] The behaviour the skill is about differs between the arms in the direction intended, visible in the artifacts.
- [ ] Anything the new arm lost from the old arm was either restored or struck on purpose.
- [ ] The release note names what was tested and what moved.
