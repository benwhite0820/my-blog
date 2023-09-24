# 把 code 推上 github

1. 先在 github 上創建一個 repository 後輸入 git init 後，把 code commit 上去，之後打下面命令
    
    ```
    git remote add origin https://github.com/<username>/<repository>.git
    
    git push -u origin master
    ```
    
2. 這時可能會遇到以下問題，這個錯誤是因為嘗試推送(commit and push)到一個分支(branch)時，遠端(remote)已經有更新，所以無法直接推送，需要先將遠端更新拉取(pull)下來，然後再進行推送。
    
    ```
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Integrate the remote changes (e.g.
    hint: 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.
    ```
    
3. 因此我們要下這個指令來把 main 拉下來
    
    ```
    git pull origin main --allow-unrelated-histories
    ```
    
4. 之後可能會進到無法逃離地獄 aka Vim 文字編輯器，像下面這樣，提醒說要打 merge 訊息，解法可以看第二塊 block
    
    ```
    Merge branch 'main' of https://github.com/benwhite0820/Omnifood
    # Please enter a commit message to explain why this merge is necessary,
    # especially if it merges an updated upstream into a topic branch.
    #
    # Lines starting with '#' will be ignored, and an empty message aborts
    # the commit.
    ```
    
    ```
    這段訊息顯示你正處於 Git 的合併（merge）狀態，Git 詢問你是否要輸入一個合併提交訊息。
    如果你確定要合併 main 分支到當前分支，你需要進行以下步驟：
    
    1. 按下 i 進入 Vim 的編輯模式，並在畫面的最上方出現 -- INSERT -- 的提示字樣，
    	 表示你現在可以輸入文字；
    2. 輸入一個合併訊息，描述這個合併的目的；
    3. 按下 Esc 鍵回到命令模式（Command mode）；
    4. 輸入 :wq（表示「write and quit」，也就是儲存並退出）；
    5. 按下 Enter 鍵，完成合併提交。
    
    如果想取消合併操作，可以按下 Esc 鍵回到命令模式，然後輸入 :q!，最後按下 Enter 鍵即可。
    ```