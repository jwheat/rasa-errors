# Introduction

Common Errors you'll see when developing with Rasa.
Please feel free to send me pull request with additions, changes or corrections.  I'd like this to be a central place to collect these types of "obscure" erorrs.

---

## AttributeError: 'str' object has no attribute 'setdefault'

```jsx
File "c:\users\jonathan\anaconda3\envs\rasa\lib\site-packages\rasa\core\domain.py", line 290, in _transform_intent_properties_for_internal_use
    properties.setdefault(USE_ENTITIES_KEY, True)
AttributeError: 'str' object has no attribute 'setdefault'
```

**Type**: Syntax Error

**File**: `domain.yml`

**Where to look**: `intents:`   - specifically `use_entities: []`

**Solution**:

- Make sure there is a space between the `:` and the `[]`
- Make sure your `use_entities: []` is indented properly under the parent intent name.
Here's an example of good indenting practices

```jsx
intents:
  - enter_data:
    use_entities: []
```

---

## AttributeError: 'bool' object has no attribute 'get'

```jsx
File "c:\users\jonathan\anaconda3\envs\rasa\lib\site-packages\rasa\core\domain.py", line 265, in collect_slots
    slot_class = Slot.resolve_by_type(slot_dict[slot_name].get("type"))
AttributeError: 'bool' object has no attribute 'get'
```

**Type**: Syntax Error

**File**: `domain.yml`

**Where to look**: `slots:`

**What the problem is**: indenting slot properties

**Solution**: you need an ever increasing set of indents for slot definitions.  Make sure your `type` and `auto_fill`, `initial_value` and any other properties of a slot are indented under the slot name

**What I've done**: I utilize past code a lot, and VSCode likes to automatically indent when you past a block that has indening, so many times my indenting is off and I'm in a rush and don't see it is incorrect. Needless to say, this error is my most frequent.

Here is an example of proper slot indentation

```jsx
slots:
  customer_first_name:
    type: unfeaturized
    auto_fill: true
```

---

## pykwalify.core - ["Value 'None' is not a dict. Value path: '/slots'"]

**Type**: Syntax Error

**File**: `domain.yml`

**Where to look**: `slots:`

**What the problem is**: indenting slot name, and/or missing `-` if your slot happens to be a list or array

**Solution**: Similar to the above error, you need an ever increasing set of indents for slot definitions with proper `-` hyphens as well.  Make sure your slot name is properly indented under the `slots:` key and double check your hyphens o be sure they're all there (or not)

**What I've done**: Sometimes even though you have your hyphens, the indents are still off.  I have some custom slots that have dictionaries (multiple hyphenated items) and sometimes I forget the `-`

Here is an example of proper slot indentation

```jsx
slots:
  risk_level:
    type: categorical
    values:
      - low
      - medium
      - high
```

---

## rasa.core.actions.action - Failed to extract slot customer_first_name with action form_change_customer_first_name

**Type**: spelling / naming error

**File**: `actions.py`, `domain.yml`

**Where to look**:  python slot code in your `actions.py` and specifically the slot names in your `domain.yml`

**What the problem is**: slot name mismatch (aka typo)

**Solution**: - this is more than likely a naming / spelling issue.  Make sure the slot name you're referencing in your [actions.py](http://actions.py) file MATCHES to the slot and/or response in your domain.yml file.  

**What I've done**: Sometimes I'll write python code for a new slot, and forget to add it to my `domain.yml` file, or I'll rename it in my head and forget to update it somewhere.

## TypeError: 'NoneType' object is not iterable

**Type**:

**File**: `actions.py`

**Where to look**:  required_slots method

**What the problem is**:

Your required slots does not return anything.  You may have some nested if logic, and chances are whatever path you just took in chat, resulted in a path that did not return anything.

**Solution**:

A quick hack, not advisable is to add a `return []` to the end of your `required_slots` method.  This catch all will always return an empty list, and satisfy the actual functionality of this method.

The better solution is to trace your if logic and handle the path you took in chat, and return an actual list (even one) of requiredSlots
