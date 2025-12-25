# 切换远端仓库
git remote -v
git branch -m  release-4.20 ocp-4.20
git remote add new-origin xxxxxx

git remote rm origin
git remote rename new-origin origin


# 合并多个提交
git rebase -i abc123^


git log -1 --format="%H %ad" --date=format:"%Y%m%d%H%M%S"
git rev-parse --short=12 HEAD
go mod edit -replace github.com/openshift/library-go=github.com/lance5890/library-go@v0.0.0-20251203010834-6d94500150a0