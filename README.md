# UnityYamlMerge Tool Demostration 2023

## Part 1: The Setup
The demostration is done on windows environment, you can chceck reference to other system via here: https://docs.unity3d.com/Manual/SmartMerge.html

UnityYamlMerge tool comes as a default tool with Unity Editor, you can find it in the Unity's Editor file path at `Editor/Data/Tools`

![WindowsEnvrionmentVariable](UnityYamlMergeDemo.png)

First add the `UnityYamlMerge` environment variable with value of where `UnityYamlMerge.exe` is located on your local machine as image shown above.

In "C:\user\<user>\.gitconfig" add the following:

```
[merge]
    tool = UnityYAMLMerge
[mergetool "UnityYAMLMerge"]
    trustExitCode = false
    cmd = UnityYAMLMerge merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
```

The above tells git that you are going to use UnityYamlMerge tool as the mergetool to resolve conflict.

Note that you still needs to setup a windows environment variable for `UnityYamlMerge` with value indicating where the tool is installed.

On windows system when we use git, usually we have git-scm installed, that comes with a Mingw64 command line system, therefore we need to add the following in git bash's `path` environment variable:
```
export UNITYYAMLMERGE="/e/UnityEditor/2021.3.0f1/Editor/Data/Tools/UnityYAMLMerge.exe" && export PATH=$PATH:$UNITYYAMLMERGE
```
Note that `/e/UnityEditor/2021.3.0f1/Editor/Data/Tools/UnityYAMLMerge.exe` should be modified to your own `UnityYamlMerge.exe`'s local path on your system.

Lastly, in your unity editor installation path we need to modify the mergespecfile.txt. `E:\UnityEditor\2021.3.0f1\Editor\Data\Tools\mergespecfile.txt` Make sure to replace the first part to your local path to unity editor.

We need to add the following code block on the top of the file, what this does is it would de fine what diff viewer to use for different file, the first 2 line specify uses `kdiff3.exe`  for `unity` and `prefab` files.  The default fall back is a `*` to represent all files that have not been caught be definition above this line to use `kdiff3.exe` as well.

```
unity use "C:\Program Files\KDiff3\kdiff3.exe" "%b" "%l" "%r" -o "%d"
prefab use "C:\Program Files\KDiff3\kdiff3.exe" "%b" "%l" "%r" -o "%d"

#
# Default fallbacks for unknown files. First tool found is used.
* use "C:\Program Files\KDiff3\kdiff3.exe" "%b" "%l" "%r" -o "%d"
```

Note that when changing this file, it might have issue with file access.  Personally I am using visual studio code, I had no issue, but friend had to change file and folder access level to allow user to edit with a default text editor.

Please also note that default location for `kdiff3.exe` installation is `C:\Program Files\KDiff3`
If you don't have kdiff3 installed you can find it at https://sourceforge.net/projects/kdiff3/

## Part 2: Merge conflict? How to resolve with UnityYamlMerge tool?

git命令行如何操作:
检查自己在feature branch,做事情的branch上
然后检查自己已经commit了所有修改的文件和代码

接着git rebase main(master) 发现merge conflict怎么办？

首先git status
看自己是在feature / 做事情的分支上，请开始你的表演
git mergetool (这个呼叫魔法师）
我们在这里设置的是UnityYamlMerge为第一优先merge 工具
这个时候会可能自动merge成功，此时
再次git mergetool一次，很快就会告诉你已经没有可以merge的了，那这个时候就可以
git rebase --continue
重复这个动作到最后一个commit节点然你检查文件和comments成功之后结束操作。

如果说你和你的朋友都修改了同样一个Object的同一个数值。这个时候就会在你第一次git mergetool之后通过kdiff3弹出友谊的选题，你是保留原文，保留自己修改，还是保留队友修该。

然后你选择题昨晚之后请选择保存文件，关闭kdiff3请继续 gitmergetool直到他提示没文件可以merge之后你就可以海皮了，集的每一次git mergetool成功之后都要手动git rebase --continue来继续下一部，如果有任何步骤不清楚请立即git rebase --abort以便重新走流程挽回之前的错误。

感谢各位大佬



