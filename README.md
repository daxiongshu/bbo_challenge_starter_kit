# Black Box Optimization Challenge

This repo contains the starter kit for the [black box optimization challenge](https://bbochallenge.com/) at [NeurIPS 2020](https://neurips.cc/Conferences/2020/CompetitionTrack).
Upload submissions [here](https://bbochallenge.com/my-submissions).

The challenge will give the participants 3 months to iterate on their algorithms.
We will use a benchmark system built on top of the AutoML challenge workflow and the [Bayesmark package](https://github.com/uber/bayesmark), which evaluates black-box optimization algorithms on real-world objective functions
For example, it will include tuning (validation set) performance of standard machine learning models on real data sets.
This competition has widespread impact as black-box optimization (e.g., Bayesian optimization) is relevant for hyper-parameter tuning in almost every machine learning project (especially deep learning), as well as many applications outside of machine learning.
The leader board will be determined using the optimization performance on held-out (hidden) objective functions, where the optimizer must run without human intervention.
Baselines will be set using the default settings of six open source black-box optimization packages and random search.

Look in `example_submissions` to see examples of submissions.
The examples currently contain the sub-directories:

```console
hyperopt/
nevergrad/
opentuner/
pysot/
random_search/
skopt/
```

![Bayesmark output](https://user-images.githubusercontent.com/28273671/66338456-02516b80-e8f6-11e9-8156-2e84e04cf6fe.png)

## Instructions for local experiments

Local experimentation/benchmarking on publicly available problems can be done using Bayesmark, which is extensively [documented](https://bayesmark.readthedocs.io/en/latest/index.html).
Note, these are not the same problems as on the leader board when submitting on the website, but may be useful for local iteration.

For convenience the script `run_local` can do local benchmarking in a single command:

```console
> ./run_local.sh ./example_submissions/pysot 3
TODO
```

The first argument gives the *folder of the optimizer* to run, while the second argument gives the number of *repeated trials* for each problem.
Set the repeated trials as large as possible within your computational budget.
For finer grain control over which experiments to run, use the Bayesmark commands directly.
See the [documentation](https://bayesmark.readthedocs.io/en/latest/readme.html#example) for details.

More explanation on how the mean score formula works can be found [here](https://bayesmark.readthedocs.io/en/latest/scoring.html#mean-scores).

## Instructions for submissions on the website

The quick-start instructions work as follows:

* Place all the necessary Python files to execute the optimizer in a folder, for example, `example_submissions/pysot`.
The site will use `optimizer.py` as the *entry-point*.
* The Python environment will be `Python 3.7.7` and contain all dependencies in [environment.txt](https://github.com/rdturnermtl/bbo_challenge_starter_kit/blob/master/environment.txt).
All other dependencies must be placed in a `requirements.txt` in the same folder as `optimizer.py`.

The submission can be prepared using the `prepare_upload` script:

```console
> ./prepare_upload.sh ./example_submissions/pysot
TODO
```

This will produce a zip file (e.g., `upload_pysot.zip`) that can be uploaded at the [submission site](https://bbochallenge.com/my-submissions).
See, `./prepare_upload.sh ./example_submissions/opentuner` for an example with a requirements file.

The submissions will be evaluated inside a docker container that has *no internet connection*.
Therefore, `prepare_upload.sh` places all dependencies from `requirements.txt` as wheel files inside the zip file.

The zip file can also manually be constructed by zipping the Python files (including `optimizer.py`) and all necessary wheel/tar balls for installing extra dependencies.
Note: the Python file should be at the top level of zip file (and not inside a parent folder).

Also note, our optimization problems have been randomly split into a set that will be used to determine the leader board, and another set that will determine the final winner once submissions are closed.

### Execution environment

The docker environment has two CPU cores and no GPUs.
It runs in `Debian GNU/Linux 10 (buster)` with `Python 3.7.7` and the pre-installed packages in [environment.txt](https://github.com/rdturnermtl/bbo_challenge_starter_kit/blob/master/environment.txt).
The optimizer has only TODO minutes on each problem.
After that, it will be cutoff from further suggestions.

### Non-PyPI dependencies

The `prepare_upload` script will only fetch the wheels for packages available on [PyPI](https://pypi.org/).
If a package is not available on PyPI you can include the wheel in the optimizer folder manually.
Wheels can be built using the command:

```bash
python3 setup.py bdist_wheel
```

as documented [here](https://packaging.python.org/tutorials/packaging-projects/#generating-distribution-archives).

## Optimizer API

Optimizer submissions should follow this template, for a suggest-observe interface, in your `optimizer.py`:

```python
from bayesmark.abstract_optimizer import AbstractOptimizer
from bayesmark.experiment import experiment_main


class NewOptimizerName(AbstractOptimizer):
    primary_import = None  # Optional, used for tracking the version of optimizer used

    def __init__(self, api_config):
        """Build wrapper class to use optimizer in benchmark.

        Parameters
        ----------
        api_config : dict-like of dict-like
            Configuration of the optimization variables. See API description.
        """
        AbstractOptimizer.__init__(self, api_config)
        # Do whatever other setup is needed
        # ...

    def suggest(self, n_suggestions=1):
        """Get suggestion from the optimizer.

        Parameters
        ----------
        n_suggestions : int
            Desired number of parallel suggestions in the output

        Returns
        -------
        next_guess : list of dict
            List of `n_suggestions` suggestions to evaluate the objective
            function. Each suggestion is a dictionary where each key
            corresponds to a parameter being optimized.
        """
        # Do whatever is needed to get the parallel guesses
        # ...
        return x_guess

    def observe(self, X, y):
        """Feed an observation back.

        Parameters
        ----------
        X : list of dict-like
            Places where the objective function has already been evaluated.
            Each suggestion is a dictionary where each key corresponds to a
            parameter being optimized.
        y : array-like, shape (n,)
            Corresponding values where objective has been evaluated
        """
        # Update the model with new objective function observations
        # ...
        # No return statement needed


if __name__ == "__main__":
    # This is the entry point for experiments, so pass the class to experiment_main to use this optimizer.
    # This statement must be included in the wrapper class file:
    experiment_main(NewOptimizerName)
```

You can replace `NewOptimizerName` with the name of your optimizer.

More details on the API can be found [here](https://bayesmark.readthedocs.io/en/latest/readme.html#id1).
Note: do not specify `kwargs` in a `config.json` for the challenge because the online evaluation will not pass any `kwargs` or use the `config.json`.

### Configuration space

TODO include space examples from proposal

## Terms and conditions

The terms and conditions for the challenge are available [here](TODO).
