## Contribute to Docs
Documentations are in **markdown** format. All changes should be made under `/docs` directory and commmited to `master` branch before deployment. 

If you add a new file, make sure to to change `mkdocs.yml`

## Publish Docs (user)
You need to fork the [repo](https://github.com/parallel-ml/docs), update locally, and submit a pull request.

You can test your local repo with (for more info check [mkdocs](https://www.mkdocs.org/))
```
mkdocs serve
```

## Publish Docs (admin)
After commit all changes to `master` branch, run the command
```
mkdocs gh-deploy
```

The `mkdocs` library will take care rest of the work and publish the docs under **Github Pages**. 