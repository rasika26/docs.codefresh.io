---
title: "Annotations"
description: "Mark your builds and projects with extra metadata"
group: codefresh-yaml
toc: true
---

Codefresh supports the annotations of several entities with custom metadata. You can use these metadata to store any optional information that you wish to keep associated with each entity. Examples would be storing the test coverage for a particular build, or a special settings file for a pipeline.

Currently Codefresh supports extra annotations for:

* Projects
* Pipelines
* Builds 
* Docker images

Documentation on how to annotate Docker images is mentioned in the [image metadata page]({{site.baseurl}}/docs/docker-registries/metadata-annotations/). In the present page we will describe the rest of the entities.

You can view/edit annotations using the [Codefresh CLI](https://codefresh-io.github.io/cli/annotations/).


## Adding annotations 

In the most basic scenario you can use the [post operations]({{site.baseurl}}/docs/codefresh-yaml/post-step-operations/) of any Codefresh [step]({{site.baseurl}}/docs/codefresh-yaml/steps/) to add annotations:

`codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
steps:
  my_custom_step:
    title: Adding annotations to a project
    image: alpine:3.9
    commands:
     - echo "Hello"
    on_success: 
      annotations:
        set:
          - entity_id: annotate-examples
            entity_type: project
            annotations:
            - my_annotation_example1: 10.45
            - my_empty_annotation
            - my_string_annotation: Hello World
{% endraw %}            
{% endhighlight %}


This pipeline adds three annotations to a project called `annotate-examples`. The name of each annotation can only contain letters (upper and lowercase), numbers and the underscore character. The name of each annotation must start with a letter.

You can view the annotations using the Codefresh CLI

```shell
codefresh get annotation project annotate-examples
```

For the `entity_id` value you can also use an actual ID instead of a name. Both `entity_id` and `entity_name` are required. The possible entity types are:

* `project` (for a project, even a different one)
* `pipeline` (for a pipeline, even a different one)
* `build` (for a build, even a different one)
* `image` (for a docker image)

Here is another example where we add annotations to another pipeline as well as the current build.

`codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
steps:
  my_custom_step:
    title: Adding annotations to multiple entities
    image: alpine:3.9
    commands:
     - echo "Hello"
    on_success: 
      annotations:
        set:
          - entity_id: my-project/my-basic-pipeline 
            entity_type: pipeline
            annotations:
            - my_annotation_example1: 10.45
            - my_empty_annotation
            - my_string_annotation: Hello World
          - entity_id: ${{CF_BUILD_ID}}
            entity_type: build
            annotations:
            - my_coverage: 70
            - my_url_example: http://www.example.com
{% endraw %}            
{% endhighlight %}

We use the `CF_BUILD_ID` variable to store annotations for the current build, but we could have used any other ID of an existing build. It is therefore possible to store annotations on any Codefresh entity (and not just the ones that are connected to the build that is adding annotations).


## Complex annotation values

Apart from scalar values, you can also store more complex expressions in annotations. You have access to all [Codefresh variables]({{site.baseurl}}/docs/codefresh-yaml/variables/), text files from the build and even evaluations from the [expression syntax]({{site.baseurl}}/docs/codefresh-yaml/expression-condition-syntax/)

`codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
steps:
  main_clone:
    title: Cloning main repository...
    type: git-clone
    repo: 'kostis-codefresh/nestjs-example'
    revision: '${{CF_REVISION}}'
  my_custom_step:
    title: Complex annotations
    image: alpine:3.9
    commands:
     - echo "Hello"
    on_finish: 
      annotations:
        set:
          - entity_id: annotate-examples/simple
            entity_type: pipeline
            annotations:
              - qa: pending
              - commit_message: ${{CF_COMMIT_MESSAGE}}
              - is_main_branch: 
                evaluate: "'${{CF_BRANCH}}' == 'main'"
              - my_json_file: "file:${{CF_VOLUME_PATH}}/path/file.json"  
              - my_docker_file: "file:Dockerfile" 
{% endraw %}            
{% endhighlight %}

>Notice that this pipeline is using dynamic git repository variables, so it must be linked to a least one [git trigger]({{site.baseurl}}/docs/configure-ci-cd-pipeline/triggers/git-triggers/) in order to work.

The last two annotations add the text of a file as a value. You can define an absolute or relative path. No processing is done on the file before being stored. If a file is not found, the annotation will still be added verbatim.
We suggest you only store small text files in this manner as annotations values.

## Removing annotations

You can also remove annotations by mentioning their name:

`codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
steps:
  my_custom_step:
    title: Adding annotations to a pipeline
    image: alpine:3.9
    commands:
     - echo "Hello"
    on_success: 
      annotations:
        set:
          - entity_id: my-project/my-basic-pipeline 
            entity_type: pipeline
            annotations:
            - my_annotation_example1: 10.45
            - my_empty_annotation
            - my_string_annotation: Hello World
            - my_second_annotation: This one will stay
  my_unit_tests:
    title: Removing annotations
    image: alpine:3.9
    commands:
     - echo "Tests failed"
     - exit 1
    on_fail: 
      annotations:
        unset:
          - entity_id: my-project/my-basic-pipeline 
            entity_type: pipeline
            annotations:
            - my_annotation_example1
            - my_empty_annotation
            - my_string_annotation
{% endraw %}            
{% endhighlight %}

You can also use both `unset` and `set` block in a single `annotations` block. And of course you can remove annotations from multiple entities.

The `unset` annotation can be used with all post-step operations (`on_success`, `on_fail`, `on_finish`).


## Adding annotations to the current build

As a convenience feature if 

1. your pipeline has a build step
1. and you want to add annotations to the present build

you can also define annotations in the root level of the build step and not mention the entity id of the build. Annotations will be added in the present build.


`codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
steps:
  main_clone:
    title: Cloning main repository...
    type: git-clone
    repo: 'kostis-codefresh/nestjs-example'
    revision: 'master'
  MyAppDockerImage:
    title: Building Docker Image
    type: build
    image_name: my-app-image
    working_directory: ./
    tag: 'sample'
    dockerfile: Dockerfile
    annotations:
      set:
        - annotations:
          - my_number_annotation: 9999
          - my_empty_annotation
          - my_docker_file: "file:Dockerfile"
          - my_text_annotation: simple_text
{% endraw %}            
{% endhighlight %}

After running this pipeline at least once, you can retrieve the metadata from any previous build by using the respective id:

```shell
codefresh get annotation build 5ce26f5ff2ed0edd561fa2fc
```
Note that this syntax is optional. You can still define metadata for a build using the post operations of any step and specifically mentioning `entity_type` as `build`.

## What to read next

* [Image annotations]({{site.baseurl}}/docs/docker-registries/metadata-annotations/)
* [Post-Step Operations]({{site.baseurl}}/docs/codefresh-yaml/post-step-operations/)
* [Creating pipelines]({{site.baseurl}}/docs/configure-ci-cd-pipeline/pipelines/)