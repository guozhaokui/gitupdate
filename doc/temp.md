        git cat-file -p master^{tree}
            这个表示看最新提交的里面的tree对象
            
        $ git cat-file -p master
        tree 00d64401783801432f370bae8bf37bdc5993b379
        parent 95cfe9853c157f9e5a1310b47dc2c271ebf504b8
        author guozhaokui <guozk@layabox.com> 1473407452 +0800
        committer guozhaokui <guozk@layabox.com> 1473407452 +0800
        
        看第一个tree的内容
        $ git cat-file -p 00d64401783801432f370bae8bf37bdc5993b379
        100644 blob e2f8adeee5a7fddb8177647d7267bb4d953d7768    .gitmodules
        100644 blob 4797790135536584c8fd7c83cf072825e88309fd    a.txt        
        
        看 parent的内容
        $ git cat-file -p 95cfe9853c157f9e5a1310b47dc2c271ebf504b8
        tree 277681efc2daa9cc4a260b479c79ca4ac56314cf
        author guozhaokui <guozhaokuiwang@gmail.com> 1442967733 +0800
        committer guozhaokui <guozhaokuiwang@gmail.com> 1442967733 +0800

        ..

        parent里面的tree
        $ git cat-file -p 277681efc2daa9cc4a260b479c79ca4ac56314cf
        100644 blob 4797790135536584c8fd7c83cf072825e88309fd    a.txt
        
        怎么查找tag
            \refs\tags
            
        创建commit
        $ echo 'first commit' | git commit-tree d8329f
            指向一个已经存在的tree
            主要包括 一个tree，日期，作者，注释
            生成一个新的sha
            
            如果再加 -p fdf4fc3 参数，表示设置parent，这样就能通过git log来看历史记录了
            git log 会同时计算差异
        ref
            .git/refs/heads/master
            指向master的最后一次提交
            git的分支就是heads中的一堆引用
            例如可以用 update-ref来创建分支
            git update-ref refs/heads/test 107ab64
            
            gc会合并refs到一起
                .git/packed-refs            
                但是如果修改的话，还是会在原来的目录中创建一个，所以，要先检查分离文件
                    9585191f37f7b0fb9444f35a9bf50de191beadc2 refs/tags/v1.1             这是一个 annotated  标签，下面^开头的就是他指向的commit
                    ^1a410efbd13591db07496601ebc7a059dd55cfe9
                HEAD
            git checkout test
                实际会修改HEAD指向对应的ref
        Tags
            tag指向一个commit，而不是tree
            git update-ref refs/tags/v1.0 cac0cab538b970a37ea1e769cbbde608743bc96d
                创建一个tag
            annotated tag 的话，会有一个tag对象
            例如 git tag -a v1.0 107ab64bd71b81e8b21adf3931d9ab8d8a125c7c -m "kkk"
            看看新创建的对象
            $ cat .git/refs/tags/v1.0
            40b63e99ec3709375460ff550ab201c23e94360d            
            看看这个对象的内容
                $ git cat-file -p 40b63e99ec3709375460ff550ab201c23e94360d
                object 107ab64bd71b81e8b21adf3931d9ab8d8a125c7c
                type commit
                tag v1.0
                tagger guozhaokui <guozk@layabox.com> 1473420727 +0800

                kkk
                            
            object指向的就是对应的commit
            当然这个实际上可以指向任何对象，例如一个文件
            
        怎么更新文件的
        
        打包对象怎么办
            根据idx分段下载？
            git verify-pack -v .git/objects/pack/pack-7a16e4488ae40c7d2bc56ea2bd43e25212a66c45.idx
            这个可以查看里面的对象的信息
        http模式
            post-receive hook的时候
                git update-server-info
            git clone http://github.com/schacon/simplegit-progit.git
            
            先取 info/refs 文件
            
            由于github全部变成https了，所以无法简单测试, curl http地址什么都不返回，curl https地址的话，提示不再支持dumb-http模式了
            
        怎么先找到changelog
            固定的tag