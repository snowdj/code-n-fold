---
layout: default
title: The Arrow - Coding Pattern?!
date: 2012-02-03
category: Programming
tags:
- bad code
- coding pattern
type: post
excerpt: One of the absolute worst code snippets I've ever stumbled upon...
---

Just as an example of what software engineers like me have to cope with sometimes. I found this nasty little bit of code inside an asp .net project we had to integrate into one of our systems. I won't post the name of the "artist" out of courtesy.

I call it "Coding pattern - **The Arrow**" - just for fun

Another thing would be "Recursions - how <em>not</em> to do it..."
Got any other name for this abomination of code let me know...
But enough of this bashing, here's the actual code in case you'd like to take a closer look...

{% highlight aspx-vb %}

Sub MakeTreeIncPx(fileContents)

    schSubFolpx objFolder,1, fileContents
    For Each objSubFolder In objFolder.SubFolders
        ' subject area
        strcurrentfolder=objsubfolder.name
        schSubFolpx objSubFolder,2, fileContents
        ' under subject area
        Set objNextFolder1 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name)
        For each nextfolder1 in objNextFolder1.SubFolders
            schSubFolpx nextFolder1,3, fileContents
            Set objNextFolder2 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name & "\" & nextfolder1.name)
            For each nextFolder2 in objNextFolder2.SubFolders
                schSubFolpx nextFolder2,4, fileContents
                Set objNextFolder3 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name & "\" & nextfolder1.name & "\" & nextfolder2.name)
                For each nextFolder3 in objNextFolder3.SubFolders
                    schSubFolpx nextFolder3,5, fileContents
                    Set objNextFolder4 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name & "\" & nextfolder1.name & "\" & nextfolder2.name & "\" & nextfolder3.name)
                    For each nextFolder4 in objNextFolder4.SubFolders
                        schSubFolpx nextFolder4,6, fileContents
                        Set objNextFolder5 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name & "\" & nextfolder1.name & "\" & nextfolder2.name & "\" & nextfolder3.name & "\" & nextfolder4.name)
                        For each nextFolder5 in objNextFolder5.SubFolders
                            schSubFolpx nextFolder5,7, fileContents
                            Set objNextFolder6 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name & "\" & nextfolder1.name & "\" & nextfolder2.name & "\" & nextfolder3.name & "\" & nextfolder4.name & "\" & nextfolder5.name)
                            For each nextFolder6 in objNextFolder6.SubFolders
                                schSubFolpx nextFolder6,8, fileContents
                                Set objNextFolder7 = objFSO.GetFolder(objFolder.path & "\" & objsubFolder.name & "\" & nextfolder1.name & "\" & nextfolder2.name & "\" & nextfolder3.name & "\" & nextfolder4.name & "\" & nextfolder5.name & "\" & nextfolder6.name)
                                For each nextFolder7 in objNextFolder7.SubFolders
                                    schSubFolpx nextFolder7,9, fileContents
                                next
                            next
                        next
                    next
                next
            next
        next
    Next
    i=0
    while i < folderNo
        i=i+1
        j=0
        if folderPx(i)=2 then
            if folderlev(i)=2 then
                fileContents.Writeline "insDoc(foldersTree, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) &  "))"
            elseif folderlev(i)=3 then
                fileContents.Writeline "insDoc(aux1, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            elseif folderlev(i)=4 then
                fileContents.Writeline "insDoc(aux2, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            elseif folderlev(i)=5 then
                fileContents.Writeline "insDoc(aux3, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            elseif folderlev(i)=6 then
                fileContents.Writeline "insDoc(aux4, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            elseif folderlev(i)=7 then
                fileContents.Writeline "insDoc(aux5, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            elseif folderlev(i)=8 then
                fileContents.Writeline "insDoc(aux6, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            elseif folderlev(i)=9 then
                fileContents.Writeline "insDoc(aux7, gLnk(" & chr(34) & "S" & chr(34) & "," & Folderpath(i) & "))"
            end if

        else 'if folderPx(i)=0 then  ' folder above px or link
            cont=0
            keeplevel=folderlev(i)
            if folderno>i then
                if folderPx(i+1)>0 and folderlev(i+1)> keeplevel then
                    cont=1
                else
                    if folderno>i+1 and folderlev(i+1)> keeplevel then
                        if folderPx(i+2)>0 and folderlev(i+2)> keeplevel then
                            cont=2
                        else
                            if folderno>i+2 and folderlev(i+2)> keeplevel then
                                if folderPx(i+3)>0 and folderlev(i+3)> keeplevel then
                                    cont=3
                                else
                                    if folderno>i+3 and folderlev(i+3)> keeplevel then
                                        if folderPx(i+4)>0 and folderlev(i+4)> keeplevel then
                                            cont=4
                                        else
                                            if folderno>i+4  and folderlev(i+4)> keeplevel then
                                                if folderPx(i+5)>0 and folderlev(i+5)> keeplevel then
                                                    cont=5
                                                else
                                                    if folderno>i+5  and folderlev(i+5)> keeplevel then
                                                        if folderPx(i+6)>0 and folderlev(i+6)> keeplevel then
                                                            cont=6
                                                        else
                                                            if folderno>i+6  and folderlev(i+6)> keeplevel then
                                                                if folderPx(i+7)>0 and folderlev(i+7)> keeplevel then
                                                                    cont=7
                                                                else
                                                                    if folderno>i+7  and folderlev(i+7)> keeplevel then
                                                                        if folderPx(i+8)>0 and folderlev(i+8)> keeplevel then
                                                                            cont=8
                                                                        else
                                                                            if folderno>i+8  and folderlev(i+8)> keeplevel then
                                                                                if folderPx(i+9)>0 and folderlev(i+9)> keeplevel then
                                                                                    cont=9
                                                                                else
                                                                                    if folderno>i+9  and folderlev(i+9)> keeplevel then
                                                                                        if folderPx(i+10)>0 and folderlev(i+10)> keeplevel then
                                                                                            cont=10
                                                                                        else
                                                                                            if folderno>i+10  and folderlev(i+10)> keeplevel then
                                                                                                if folderPx(i+11)>0 and folderlev(i+11)> keeplevel then
                                                                                                    cont=11
                                                                                                else
                                                                                                    if folderno>i+11  and folderlev(i+11)> keeplevel then
                                                                                                        if folderPx(i+12)>0 and folderlev(i+12)> keeplevel then
                                                                                                            cont=12
                                                                                                        else
                                                                                                            if folderno>i+12  and folderlev(i+12)> keeplevel then
                                                                                                                if folderPx(i+13)>0 and folderlev(i+13)> keeplevel then
                                                                                                                    cont=13
                                                                                                                else
                                                                                                                    if folderno>i+13  and folderlev(i+13)> keeplevel then
                                                                                                                        if folderPx(i+14)>0 and folderlev(i+14)> keeplevel then
                                                                                                                            cont=14
                                                                                                                        else
                                                                                                                            if folderno>i+14  and folderlev(i+14)> keeplevel then
                                                                                                                                if folderPx(i+15)>0 and folderlev(i+15)> keeplevel then
                                                                                                                                    cont=15
                                                                                                                                else

                                                                                                                                end if  
                                                                                                                            end if
                                                                                                                        end if  
                                                                                                                    end if
                                                                                                                end if  
                                                                                                            end if
                                                                                                        end if  
                                                                                                    end if
                                                                                                end if  
                                                                                            end if
                                                                                        end if  
                                                                                    end if
                                                                                end if  
                                                                            end if  
                                                                        end if  
                                                                    end if
                                                                end if  
                                                            end if
                                                        end if  
                                                    end if
                                                end if
                                            end if
                                        end if
                                    end if
                                end if
                            end if
                        end if
                    end if
                end if
            end if
            if cont>0 then
                if folderlev(i)=2 then
                    fileContents.Writeline "aux1=insFld(foldersTree, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 3 then
                    fileContents.Writeline "aux2=insFld(aux1, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 4 then
                    fileContents.Writeline "aux3=insFld(aux2, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 5 then
                    fileContents.Writeline "aux4=insFld(aux3, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 6 then
                    fileContents.Writeline "aux5=insFld(aux4, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 7 then
                    fileContents.Writeline "aux6=insFld(aux5, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 8 then
                    fileContents.Writeline "aux7=insFld(aux6, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                elseif folderlev(i) = 9 then
                    fileContents.Writeline "aux8=insFld(aux7, gFld(" & chr(34) & FolderName(i) & chr(34) & "," & chr(34) & "javascript:parent.op()" & chr(34) & "))"
                end if
            end if
        end if  
        'if folderPx(i)=1 then   'px folder
        if pxName(i) <> "" then

            if folderlev(i)=2 then
                fileContents.Writeline "insDoc(foldersTree, gLnk(" & chr(34) & "S" & chr(34) & ","  & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=3 then
                fileContents.Writeline "insDoc(aux1, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=4 then
                fileContents.Writeline "insDoc(aux2, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=5 then
                fileContents.Writeline "insDoc(aux3, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=6 then
                fileContents.Writeline "insDoc(aux4, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=7 then
                fileContents.Writeline "insDoc(aux5, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=8 then
                fileContents.Writeline "insDoc(aux6, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            elseif folderlev(i)=9 then
                fileContents.Writeline "insDoc(aux7, gLnk(" & chr(34) & "S" & chr(34) & "," & chr(34) & pxName(i) & chr(34) & "," & chr(34) & FolderPath(i) & "))"
            end if
        '   end if

        end if
    wend

End Sub

{% endhighlight %}
