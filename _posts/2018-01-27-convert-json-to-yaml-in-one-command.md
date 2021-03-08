---
title: 'Convert JSON to YAML in one command'
date: '2018-01-27T20:57:56+10:00'
permalink: /convert-json-to-yaml-in-one-command
author: 'James Elsey'
category:
    - 'Cloud'
tag:
    - aws
    - cloudformation
    - json
    - yaml

---
I’ve been playing around with AWS CloudFormation recently, which supports both JSON and YAML. I prefer to use YAML, but a lot of the examples I was looking at were based in JSON. Fortunately there’s a quick way to convert a JSON file to it’s YAML equivalent.

```ruby
ruby -ryaml -rjson -e 'puts YAML.dump(JSON.load(ARGF))'  linux-bastion.yml
```

Can’t remember where I found it, likely StackOverflow, anyway it’s a useful command to have!