Clearing your cached rsessions

Check size with `du -h /home/rstudio/.local/share/rstudio/sessions` and `du -h /home/rstudio/[project_name]/.Rproj.user/*`


Remove large session files in .local

```
rm -r /home/rstudio/.local/share/rstudio/sessions
```
