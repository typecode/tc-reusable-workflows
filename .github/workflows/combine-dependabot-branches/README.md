# Workflow: Combine Dependabot Branches
[Dependabot](https://github.com/dependabot) is a tool which makes dependency updating more painless. However, Dependabot creates a new branch and PR for each dependency it is going to update, which often times results in a LOT of PRs. If you perform maintenance on your applications on a schedule, you may want to combine these branches and PRs into one to review all updates at once. This workflow effectively combines all of the open branches with a specified prefix, and updates an existing or opens a new PR to describe all of the contained Dependabot updates.

## How To Use
This workflow takes three inputs
  - `branchPrefix`: the prefix to look for in branches to combine, i.e. "dependabot"
  - `combineBranchName`: the name of the branch to combine all the dependabot branches into
  - `ignoreLabel`: a label that will be looked for on all branches with `branchPrefix`. if the label is on the branch in question, it will be skipped in the merging process

Here is an example of how to call the reusable workflow within your own workflow:
```yaml
      - uses: typecode/typecode-shared-workflows/.github/workflows/combine-dependabot-branches/combine-dependabot-branches@master.yml
        with:
          branchPrefix: ${{ env.BRANCH_PREFIX }}
          combineBranchName: ${{ env.COMBINE_BRANCH_NAME }}
          ignoreLabel: ${{ env.IGNORE_LABEL }}
```
**NOTE**: Inputs don't have to be environment variables, they can be passed in any way you choose (hard coded, as inputs to your own workflow, etc.)

For more information on calling reusable workflows, see the [GitHub Documentation](https://docs.github.com/en/actions/using-workflows/reusing-workflows#calling-a-reusable-workflow)

## Limitations
From the [repo](https://github.com/hrvey/combine-prs-workflow) this workflow is based off of:
>This workflow merges the branches of the PRs together into a single branch using git's simple merge and automatic merge strategies. Unfortunately that means it has the same limitations as these merge strategies, so it will fail if two or more PRs have branches that cannot be auto-merged due to a merge conflict - it will say something along the lines of "Auto-merge failed" or "Should not be doing an octopus" (see an example [here](https://github.com/hrvey/combine-prs-workflow/issues/2)). The way this will typically happen when updating dependencies is that different dependency updates end up modifying the same line in a .lock file (the file that ensures stability in exactly which versions of depencies are currently being used, when dependencies are defined in a broad enough way that multiple different versions could satisfy the constraints). This typically happens if they share some common third dependency - for example if some modular framework has components A and B, that both depend on C, then updating A and B independently might lead to also indirectly updating C to two different versions in the two branches, and that will prevent them from being mergable.
>The "correct" solution here is to add both dependencies together, then let the package manager resolve the dependencies to hopefully find a version of C to put in the .lock file that will satisfy both A and B. If you find yourself in need of this, then this workflow won't help you, and you should consider switching to a dependency update service that will update dependencies together like that (at the moment Dependabot does not, but Depfu and Renovate do).
>If it's only a single PR out of several that is causing merge issues, you can use the label 'nocombine' to merge the others together, then merge the last one alone.

## Credits
This workflow is based off of [this](https://github.com/hrvey/combine-prs-workflow) existing, public workflow with modifications