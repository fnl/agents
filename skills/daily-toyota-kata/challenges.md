# Challenge structure

Read the relevant file in the `templates/` folder before creating a note or log entry, and replace its placeholders. Log entries on every page are in temporal order, oldest first.

- **Naming:** a challenge or target folder and its page share the same name (`<name>/<name>.md`); the page *title* may be longer.
- **Challenge:** `challenges/<challenge>/<challenge>.md` from `templates/challenge.md`: a measurable, solution-free goal and red lines. List its targets under the *Targets* section.
- **Target:** `challenges/<challenge>/<target>/<target>.md` from `templates/target.md`: measurable condition, deadline, constraints, current condition.
- **Obstacle:** link it from the target's *Obstacles* section. Create its page `challenges/<challenge>/<target>/<obstacle>.md` from `templates/obstacle.md` once the first experiment, meeting, or message is logged against it; until then, the link alone is fine. Use a bulleted list of obstacle links on the target page, not tasks.
	- **Resolving obstacles:** set property `resolved: true` on the obstacle page and move its link to the target's *Resolved Obstacles* section.
- **Other files:** a target folder may also hold supporting notes, instructions, diagrams, and attachments. Only pages linked from the target's obstacle sections (or carrying the `resolved` property) are obstacles.
- **Activity log:** record experiments, meetings, and messages on the obstacle pages using `templates/experiment.md`, `templates/meeting.md`, and `templates/message.md`. Write the prediction before the outcome; the **learning** *is the delta between prediction and actual outcome*.
	- **Meeting prep:** for deeper preparation, insert `templates/meeting-prep.md` before the meeting's Agenda: what the user should share, the needs and interests behind the meeting, and assumptions about participants' needs and interests to test.
- **Missing pages:** when a folder or link implies a missing challenge or target page, create it if its content is clear from the existing notes; otherwise wait for a review session.
