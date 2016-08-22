# Using Dependencies

## Berks

Run `berks install`

Berks looks at the source of cookbooks (i.e. Supermarket) and makes available to you all of the cookbooks that you tell it in your `metadata.rb` file with the `depends` resource.

When you converge your `depends` resources, it creates a `Berksfile.lock` file which lists all of the dependencies from the other cookbooks from which your available cookbooks draw. 

To run those cookbooks, go to the `default.rb` file in `recipes` and add the recipes from those dependent cookbooks that you need to run using the `include_recipe` resource.  