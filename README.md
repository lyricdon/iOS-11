# iOS-11
pod in iOS 11 

记录一下最近ios11 遇到的问题

### 1.如果使用CoacaPods, AppIcon会无法正常显示,并且导致打包提交时提示 " 120x120pixel appIcon... " 的错误
  解决方法:  复制如下代码到Podfile中, pod update 再重新运行或者打包就行.
 ```
target 'Test' do
	pod 'Fabric'
  post_install do |installer|
   	copy_pods_resources_path = "Pods/Target Support Files/Pods-ArtCalendar/Pods-ArtCalendar-resources.sh"
   	string_to_replace = '--compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}"'
   	assets_compile_with_app_icon_arguments = '--compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}" --app-icon "${ASSETCATALOG_COMPILER_APPICON_NAME}" --output-partial-info-plist "${BUILD_DIR}/assetcatalog_generated_info.plist"'  
    text = File.read(copy_pods_resources_path)
   	new_contents = text.gsub(string_to_replace, assets_compile_with_app_icon_arguments)
   	File.open(copy_pods_resources_path, "w") {|file| file.puts new_contents }
   end
 end
  ```
  
  如果有多个Target, 打开工程目录下:[工程名]/Pods/Target Support Files/Pods-[工程名]/Pods-[工程名]-resources.sh ,将上一步生成的
  ```
  printf "%s\0" "${XCASSET_FILES[@]}" | xargs -0 xcrun actool --output-format human-readable-text --notices --warnings --platform "${PLATFORM_NAME}" --minimum-deployment-target "${!DEPLOYMENT_TARGET_SETTING_NAME}" ${TARGET_DEVICE_ARGS} --compress-pngs --compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}" --app-icon "${ASSETCATALOG_COMPILER_APPICON_NAME}" --output-partial-info-plist "${BUILD_DIR}/assetcatalog_generated_info.plist"
fi
```
  替换掉对应的工程下相同路径,文件的最后一行 `printf ... fi`
  
### 2. NavigateBarItem变形
  NavigateBar左右默认会有20的边距, 如果'NavigateBarItem'为'CustomView', 如果使用了图片,会在约束内自动拉伸item到图片大小,改变布局.需要修改image的大小到'CustomView'的大小, 而在ios11以前是不需要的
  
