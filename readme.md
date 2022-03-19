##genbabels.py
musicxml_obj.write_sequences()
进入以下文件：
###musicxml.py
###write_sequences()
有这么一个重要的变量：
sequences = self.get_sequences()
sequences是一个列表，每一项对应着乐谱图片的一行
在.musicxml文件中对应的是new-page属性（也就是出现一个new-page属性就另起一个sequence的项）
每一个sequence的列表项目对应着一个输出的.semantic文件
所以一行乐谱图片对应着一个.semantic文件
###get_sequences()
有这么几个重要的变量：
num_staves(int类型的变量)这个是指有几个声部，钢琴乐谱当中是在staff-layout的number里面表示，架子鼓乐谱是如果出现了<top-system-distance>就说明有两个声部（好像是这么回事），目前架子鼓乐谱暂时按照单声部来处理

staves(列表型的变量)每一个列表项记录着这个声部分别对应的输出

for循环

    measure是.musicxml文件里面的<measure>，一个for循环处理一个measure
    new_page(int类型的变量)标志是否开始了新的一行（是否进入下一个sequence项）
    --在下面会具体说到--measure_staves, skip = self.read_measure(measure, num_staves, new_page, staves, new_score) 根据measure生成measure_staves
    staves[j] += measure_staves[j]把measure_staves里面的东西添加到staves里面
    在下一个for循环的一开始的时候出现sequences.append(staves[0]+"+++ "+staves[1])这么一句，就是把上一波for循环时候的staves添加到sequences里面
    
###read_measure(self, measure, num_staves, new_page, cur_staves, new_score)
注意read_measure实际参数当中的staves变成了形式参数当中的cur_staves，所以在这个函数里面的staves和这个函数外面的staves不是一个staves
有这么几个重要的变量
staves(列表类型的变量，记录了每个声部的输出)
voice_lines(字典类型，记录了每个声部的输出)
voice_durations(字典类型，记录了每个小节（measure）的时间间隔（duration）)
cur_elem记录当前的elem的信息
for循环

    measure中的每个elem：
    cur_voice记录当前的是哪个声部
    if elem的标签是attributes
    elif elem的标签是note:
        cur_elem,is_chord,voice,duration,is_grace,_ = m.parse_note(elem)
        在voice_lines和voice_durations里面添加信息
    elif elem的标签是forward:
        forward_dur(列表类型，记录关于forward标签的信息)，往forward_dur里面添加信息
    elif elem的标签是backup:
        往上一个声部的voice_line里面添加信息（注意之前是voice_lines[voice]，现在是voice_lines[cur_voice]）
        往上一个声部的voice_durations里面添加关于forward_dur的信息
    for循环
        往staves里面添加cur_elem的信息
    for循环
        如果cur_elem中出现了key,clef,time这样的乐谱非正文的信息，往voice_lines和voice_durations里面添加信息
    musicxml.py文件的457行到551行我现在没太看懂
    对于多声部（voice_line和staves都是有大于1项），在voice_line和staves的第一项，在前面加上key-signature,clef,time这些信息
    对于单声部，直接在voice_line和staves前面加上key-signature,clef,time这些信息
    return stave（也就是上一个函数的measure_staves）
###measure.py文件
这个文件是把各种标签转化成一些东西，返回到musicxml.py中的read_measure函数当中
##musicxml.py文件的457行到551行我现在没太看懂
