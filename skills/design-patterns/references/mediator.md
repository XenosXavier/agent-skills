# Mediator

**Intent:** Centralize complex communication between objects in a single mediator so peers stay decoupled from each other.

## Structure

```
interface Mediator
    notify(sender: Colleague, event: String)

class ConcreteMediator implements Mediator
    colleagueA: ColleagueA
    colleagueB: ColleagueB

    notify(sender, event)
        if sender == colleagueA and event == "X"
            colleagueB.react()
        else if sender == colleagueB and event == "Y"
            colleagueA.react()

abstract class Colleague
    mediator: Mediator
    trigger(event)
        mediator.notify(this, event)
```

## Example

```
// Game lobby UI: ready checkbox, class dropdown, and start button
// are interdependent — LobbyMediator coordinates all interactions

class LobbyMediator implements Mediator
    readyCheckbox: CheckboxControl
    classDropdown: DropdownControl
    startButton: ButtonControl

    notify(sender, event)
        if sender == readyCheckbox and event == "toggled"
            startButton.setEnabled(allPlayersReady())
            if readyCheckbox.isChecked
                classDropdown.setEnabled(false)

        if sender == classDropdown and event == "changed"
            previewPanel.show(classDropdown.selectedClass)

        if sender == startButton and event == "clicked"
            startGameSession()

class CheckboxControl extends Colleague
    toggle()
        checked = !checked
        trigger("toggled")

class DropdownControl extends Colleague
    select(option)
        selectedClass = option
        trigger("changed")

class ButtonControl extends Colleague
    click() -> trigger("clicked")

// Wiring
mediator = new LobbyMediator()
mediator.readyCheckbox = new CheckboxControl(mediator)
mediator.classDropdown = new DropdownControl(mediator)
mediator.startButton   = new ButtonControl(mediator)
```

## Trade-offs

+ Colleagues only know the Mediator interface — zero peer-to-peer coupling
+ Centralizes interaction logic in one place, easy to read and modify
- Mediator becomes a god object if coordination logic grows unchecked
- A bug in the mediator can break the entire interaction graph at once

## Related

- **Facade** — Facade is one-directional simplification; Mediator enables two-way peer coordination
- **Observer** — Mediator can use Observer internally so colleagues publish events instead of calling the mediator directly
- **Command** — colleague requests to the mediator can be Commands to support logging or undo
