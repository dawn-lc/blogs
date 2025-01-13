```yaml
- name: 获取TagSHA
  run: |
    if git rev-parse --verify ${{ env.tag }} >/dev/null 2>&1; then
      echo "tag_sha=$(git rev-list -n 1 ${{ env.tag }})" >> $GITHUB_ENV
    else
      echo "::error Tag '${{ env.tag }}' does not exist"
      exit 1
    fi
```
使用：
```yaml
${{ env.tag_sha }}
```
