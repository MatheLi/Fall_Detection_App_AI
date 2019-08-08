## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/MatheLi/BWKI/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/MatheLi/BWKI/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.







































Structure
1.	Introduction
2.	How does the system work?
3.	Test attempts
a.	First model
b.	Test results of the first model
c.	Optimization, second model
d.	Test results of the second model
e.	Further improvement options
4.	Problems and their solutions while the project
5.	Outlook









1.	Introduction
Falls those leads to fatal injury have become a great challenge that cannot be neglected for elderly people. In the last few years, different kinds of approaches have been proposed in fall detection area, which can be explained and categorized into three types: wearable device based, ambience sensor based and vision based [1]. First of all, wearable devices usually take advantages of embedded sensors to detect the motion and location of the body, such as accelerometer, magnetometer and gyroscope [2, 3]. And the cost of wearable device based approach is quite low, as well as the installation and operation is not complicated for the elderly. Secondly, ambience based approach always use pressure sensors to detect and track body. This solution is also cost-effective and easy-deployment [4, 5]. However, the possibility of sensing objects other than human bodies posts a remarkable challenge to the detection accuracy of this approach. Last but not least, vision based solution makes full use of deployed cameras to monitor all the objects within the range, including human bodies [6, 7]. There is less intrusion into people’s daily life than the above two approaches, while the observation space is limited and ubiquitous monitoring can’t be achieved.
 
In this project, a surveillance system based on Arduino, Windows 10 and Microsoft Azure for real-time fall detection is proposed. Raw data of three-dimensional accelerometer are provided by Arduino with ADXL345. Windows 10 device makes use of this information to get the orientation of the subject by efficient data fusion and fall detection algorithms. Microsoft Azure services and Mobile/PC application are also designed to achieve seamless data processing, analyzing, storing and acquiring any time from any place as long as they have access to the Internet. The system architecture is shown is Fig. 1 as follows. 
 
 https://create.arduino.cc/projecthub/JiongShi/a-fall-detection-system-based-on-arduino-windows-and-azure-2c03cf

2.	How does the system work?
That’s why we decided to create a smartphone-app which detects falls. The app must be installed and activated once and is running since then. If it detects a fall (unusual rotation of the phone or speed) it opens the app itself and is asking if the fall was detected right. When no answer after 30 seconds follows the app is sending an emergency call with the actual position of the phone. If the person is fallen but still can move or hasn’t any bigger injury the person can click on the button “Deactivate progress”, where she deactivates the time counter before the program is sending the emergency call. In case of a false fall detection of the program there is another button “False detection” which if it is pressed is not only stopping the time counter but also is giving feedback to the program. In this way the AI is still learning and learning. 




















3.	Test attempts
a.	First model
Text about the first easy model, why have we done this model first.
Check our code on GitHub (link)
b.	Test results of the first model
Short text about results and why we think why they are as they are
Check our results on GitHub (link)
c.	Optimization, second model
What has been changed and why
Check our results on GitHub (link)
d.	Test results of the second model
Short text about results and why we think why they are as they are
Short comparison of the two models and the progress from model 1 to model 2
e.	Further improvement options
Idea of a next model or what and where can we improve next?
4.	(Problems and their solutions while the project)
…
5.	Outlook
Next steps
