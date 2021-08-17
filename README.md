
1) AVFormatContext - არის სტრუქტურა რომელიც ინახავს თვითონ ვიდეო სტრიმი რა ფორმატის კონტეინერში დევს.

2) avformat_alloc_context() ამ ფუნქციით ხდება AVFormatContext სტრუქტურის ალოკაცია და აბრუნებს AVFormatContext ამ სტრუქტურის პოინტერს.

3) avformat_open_input(pFormatContext, filename, NULL, NULL) ეს ფუნქცია ხსნის ვიდეო ფაილს რომელის pathname მითითებულია მეორე პარამეტრად

და კითხულობს ვიდეოს ჰიდერს შემდგომ მაგ ჰიდერში რა ინფორმაციაც ეწერა ვიდეოზე ავსებს სტრუქტურას რომელიც მითითებულია პირველ პარამეტრად(სტრუქტურის პოინტერი გდაეცემა)

4) pFormatContext->iformat->long_name არის სტრუქტურის წევრი რომელიც ინახავს ფორმატის სახელს
   pFormatContext->duration არის ასევე სტრუქტურის წევრი რომელიც ინახავს ვიდეოს ხანგრძლივობას

5) ვიდეოში ვიცით რომ გვაქვს აუდიო და ვიდეო სტრიმები ამ სტრიმების წაკითხვა რომ შეგვეძლოს
    avformat_find_stream_info(&pFormatContext,  NULL) - ფუნქცია გვაძლევს საშუალებას რომელსაც პირველ პარამეტრად გადაეცემა AVFormatContext
    ტიპის სტრუქტურა სადაც უკვე წერია ინფორმაცია ვიდეოს შესახებ(ვიდეოს ჰიდერი).

    pFormatContext->nb_streams არის სტრიმების რაოდენობა ვიდეოში
    pFormatContext->streams[i] არის თვითონ სტრიმი 
    რომლებსაც შეგვიძლია ლუპით დავუაროთ :

    for (int i = 0; i < pFormatContext->nb_streams; i++)
    {
    //
    }


6)  AVCodecParameters არის სტრუქტურა რომელიც ინახავს თითოეული სტრიმის კოდეკის პარამეტრებს(ქონებას)

    pFormatContext->streams[i]->codecpar ში არის კოდეკის პარამეტრები რომელსაც შემდგომ მინავიჭებთ
    AVCodecParameters სტრუქტურას.
 
7) avcodec_find_decoder(pLocalCodecParameters->codec_id) ეს ფუნქცია პოულობს კოდეკს რომელიც გამოყენებულია ამ ვიდეოს
    ენკოდინგისთვის codec_id  წევრის მიხედვით რომელიც არის AVCodecParameters ტიპის სტრუქტურის წევრი.
    და აბრუნებს AVCodec სტრუქტურის პოინტერს. ამ კომპონენტის საშუალებით უნდა გაუკეთდეს encode და decode სტრიმს.

8) pLocalCodecParameters არის AVCodecParameters სტრუქტურის ტიპის pLocalCodecParameters->codec_type წევრი რომელის მიხედვითაც მოწმდება 
    თითოეული სტრიმი რომელ კოდეკს მოიცავს, თუ მისი მნიშვნელობა არის AVMEDIA_TYPE_VIDEO მაშინ ჩვენ გვაქვს ვიდეო კოდეკთან შეხება
    ანუ ვიდეო სტრიმი გვაქვს ამოღებული ამის მიხედვით შეგვიძლია შემდგომ მივწვდეთ ვიდეო კოდეკის პარამეტრებს
    pLocalCodecParameters->width ამ წევრით შეგვიძლია გავიგოთ ვიდეოს სიგანე pLocalCodecParameters->height ამ წევრით კი სიმაღლე

    წინააღმდეგ შემთხვევაში თუ pLocalCodecParameters->codec_type მნიშვნელობა არის AVMEDIA_TYPE_AUDIO
    ესეიგი გვაქვს აუდიო სტრიმიდან ამოღებული ინფო რომელიც მოიცავს აუდიო კოდესკ 
    pLocalCodecParameters->channels ამ წევრით ვწვდებით აუდიოს არხების რაოდენობას  pLocalCodecParameters->sample_rate ამ წევრით სემპლების რაოდენობას წამში.

    pLocalCodec->long_name ამ წევრის საშუალებით ვიგებთ კოდეკის სახელს.
    pLocalCodec->id ამ წევრის საშუალებით ვიგებთ კოდეკის აიდის.
    pLocalCodecParameters->bit_rate ამ წევრით ვიგებთ ბიტ რეითს (რამდენი ბიტი გვაქვს გარკვეულ დროში)

9)  AVCodecContext სტრუქტურაში ინახება კოდეკის შესახებ დაამატებითი ინფორმაცია ვრცლად
    რომლის შევსებასაც ვახდენთ avcodec_parameters_to_context(pCodecContext, pCodecParameters) ფუნქციით
    რომელსაც პირველ პარამეტრად გადაეცემა სტრუქტურის პოინტერი რომელიც უნდა შეავსოს მეორე პარამეტრად გადაცემული სტრუქტურის მიხედვით
    როდესაც ჩვენ უკვე შევავასებთ AVCodecContext სტრუქტურას შემდგომ უნდა გავხსნათ ეს კოდეკი და გამოვიყენოთ
    avcodec_open2(pCodecContext, pCodec, NULL) ამ ფუნქციის გამოყენებით რომელსაც პირველ პარამეტრად გადაეცემა კოდეიკის კონტექსტის სტრუქტურის პოინტერ
    და მეორე პარამეტრად კოდეკის პარამეტრების სტრუქტურის პოინტერი.

10) ახლა ჩვენ უნდა დავდგეთ და წავიკითხოთ სტრიმები შემდგომ დეკოდინგიც უნდა გავუკეთოთ თითოეული კადრისთვის(frame - თვის)
    უნდა გამოვყოთ მეხსიერება ორი კომპონენტისთვის AVPacket - თვის და AVFrame - თვის
    რომელსაც აკეთებენ შემდეგი ფუნქციები:

    AVPacket *pPacket = av_packet_alloc() // გამოიყოფა მეხსიერება AVPacket სტრუქტურისთვის
    AVFrame *pFrame = av_frame_alloc()  // გამოიყოფა მეხსიერება AVFrame სტრუქტურისთვის

    ვიდეოდან პაკეტის წაკითხვა ხდება av_read_frame(pFormatContext, pPacket) ფუნქციით რომელსაც პირველ პარამეტრად გადაეცემა 
    AVFormatContext სტრუქტურის პოინტერი და წერს AVPacket ტიპის სტრუქტურაში რომელის პოინტერიც
    მეორე პარამეტრად გადაეცემა ფუნქციას

    პაკეტების წაკითხვა ხდება შემდეგი გზით:
```cpp
    while (av_read_frame(pFormatContext, pPacket) >= 0) 
    {
    //...
    }
```
11) ეხლა ჩვენ უნდა ამოვიღოთ ენკოდირებული(კომპრესირებული) კადრი(frame) და ჩავდოთ AVPacket - ში  
    კოდეკ კონტექსტის მეშვეობით avcodec_send_packet(pCodecContext, pPacket) ფუნქციის გამოყენებით
    სადაც პირველი პარამეტრი არის AVCodecContext სტრუქტურის პოინტერი და მეორე პარამეტრი AVPacket სტრუქტურის პოინტერი სადაც ჩაიდება მთლიანი პაკეტი.

12) რომ ამოვიღოთ დეკოდირებული (შეუკუმშავი, დაუკომპრესირებელი) კადრი(frame) კოდეკიდან 
    გვჭირდება avcodec_receive_frame(pCodecContext, pFrame) ფუნქცია რომელსაც გადაეცემა კოდეკ კონტექსტი
    და AVFrame კადრის სტრუქტურის პოინტერი

13) pCodecContext->frame_number სტრუქტურის წევრით ვიღებთ კადრის ნომერს
    pFrame->pts ამ წევრით pts - ს
    pFrame->pkt_dts ამით დტს - ს
    pFrame->key_frame ფრეიმისი გასაღებს
    pFrame->coded_picture_number ენკოდირებული ფოტოს ნომერს რომლის დეკოდირებაც უნდა მოხდეს ნომერის მიხედვით
    pFrame->display_picture_number დეკოდირებული ფოტოს ნომერს რომლის ჩვენებაც უნდა მოხდეს ნომერის მიხედვით

14) pFrame->data[0] წევრით ვწვდებით კადრის თითიოეულ ბაიტს 
    pFrame->width წევრით კადრის სიგანეს
    pFrame->pFrame->height წევრით კადრის სიმაღლეს

    
    MUXING:

    ამ ნაწილში განვიხილავთ მუქსინგის(muxing) ნაწილს.

    ყველა ვიდეო შედგება სხვადასხვა ტიპის კონტეინერისგან რომელშიც ჩალაგებულია
    აუდიო და ვიდეო სტრიმები.
    
    კონტეინერები შეიძლება სხვადსახვა სახის იყოს:

    3GP, ADTS, FLAC, MPEG / MPEG-2, MPEG-4 (MP4) და ა.შ.

    მუქსინგის დანიშნულება არის ვიდეოს ერთი კონტეინერიდან მეორეში გადაყვანა.
    აქ განვიხილავთ როგორ ხდება ეს ყველაფერი.

    უნდა შევქმნათ 2 ფაილი ინპუტისთვის(input) და აუტპუტისთვის(output) 
    და გავაკეთოთ თითოეულისთვის ვიდეო ფორმატის კონტექსტების სტრუქტურა.

    AVFormatContext *input_format_context = NULL
    AVFormatContext *output_format_context = NULL

    გავხსნათ ინპუტ ფაილი:
```cpp
    if ((ret = avformat_open_input(&input_format_context, in_filename, NULL, NULL)) < 0) 
    {
        fprintf(stderr, "Could not open input file '%s'", in_filename);
        goto end;
    }
```
    avformat_open_input(&input_format_context, in_filename, NULL, NULL) ფუნქციით ვხსნით ინპუტ ფაილს 
    და სტრუქტურაში ვწერთ ამ ვიდეოს ჰიდერს მთლიან ინფორმაციას კოდეკებზე სტრიმებზე და ასე შემდეგ.
```cpp
    if ((ret = avformat_find_stream_info(input_format_context, NULL)) < 0)
    {
        fprintf(stderr, "Failed to retrieve input stream information");
        goto end;
    }
```
    avformat_find_stream_info(input_format_context, NULL) ფუნქციით ვიღებთ სტრიმის ინფორმაციას და ვდებთ იმავე სტრუქტურაში


    avformat_alloc_output_context2(&output_format_context, NULL, NULL, out_filename) ფუნქციით ვხსნით აუტპუტ ფაილს
    და სტრუქტურას ვქმნით რომელის ალოკაციაც მოხდება და ინიციალიზაცია საჭირო წევრების.
```cpp
    if (!output_format_context) 
    {
        fprintf(stderr, "Could not create output context\n");
        ret = AVERROR_UNKNOWN;
        goto end;
    }    
```
    ამ ნაწილით ვამოწმებთ რამე შეცდომა თუ მოხდა სტრუქტურის ალოკაციის ან ინიციალიზაციის დროს.

    number_of_streams = input_format_context->nb_streams; სტრიმების რაოდენობა
    streams_list = av_mallocz_array(number_of_streams, sizeof(*streams_list)); სტრიმების ლისტის დაბრუნება

    სტრიმების ლისტის დაბრუნება გვჭირდება რადგან სტრიმი შეიძლება აუდი ვიდეოს და სუბტიტრების გარდა სხვაც იყოს
    მაგრამ ამჯერად ჩვენ არ გვჭირდება ამ სამის გარდა სხვა სტრიმი.

    ჩვენ ეხლა ყველა აუთ სტრიმი უნდა ჩავდოთ აუტპუტ ვიდეოს კონტექსტ სტრუქტურაში.
```cpp
    for (i = 0; i < input_format_context->nb_streams; i++)
    {
        AVStream *out_stream;
        AVStream *in_stream = input_format_context->streams[i];
        AVCodecParameters *in_codecpar = in_stream->codecpar;

        if (in_codecpar->codec_type != AVMEDIA_TYPE_AUDIO &&
            in_codecpar->codec_type != AVMEDIA_TYPE_VIDEO &&
            in_codecpar->codec_type != AVMEDIA_TYPE_SUBTITLE) {
            streams_list[i] = -1;
            continue;
        }

        streams_list[i] = stream_index++;
        out_stream = avformat_new_stream(output_format_context, NULL);

        if (!out_stream)
        {
            fprintf(stderr, "Failed allocating output stream\n");
            ret = AVERROR_UNKNOWN;
            goto end;
        }

        ret = avcodec_parameters_copy(out_stream->codecpar, in_codecpar);

        if (ret < 0)
        {
            fprintf(stderr, "Failed to copy codec parameters\n");
            goto end;
        }
    }
```
    ამ კოდის ფრაგმენტით ჩვენ შეგვიძლია ჩამოვუაროთ თითოეულ სტრიმს
    ვნახოთ თუ რა ტიპის სტრიმია თუ ჩვენთვის სასურველი სტრიმია მაშინ
    აუტპუტ სტრიმები უნდა ჩავდოთ ჩვენს ვიდეო ფრომატ კონტექსტ სტრუქტურაში
    ამ ყველაფრის გაკეთება შესაძლებელია avformat_new_stream(output_format_context, NULL) ფუნქციით
    შემდგომ იმპუტ სტრიმების კოდეკის პარამეტრებს ვაკოპირებთ და ვსვავთ მეორე ვიდეოს კოდეკის პარამეტრებში
    რადგან შიგნით სტრიმები ერთნაირად იქნება ენკოდირებული(სტრიმების ფორმატები იგივეა) და მხოლოდ კონტეინერს ვცლით ჩვენ.

    ამ კოდის ფრაგმენტით ჩვენ შევქმნით აუტპუტ ფაილს:
```cpp
    if (!(output_format_context->oformat->flags & AVFMT_NOFILE)) 
    {
        ret = avio_open(&output_format_context->pb, out_filename, AVIO_FLAG_WRITE);
        if (ret < 0) {
            fprintf(stderr, "Could not open output file '%s'", out_filename);
            goto end;
        }
    }

    ret = avformat_write_header(output_format_context, NULL);

    if (ret < 0) 
    {
        fprintf(stderr, "Error occurred when opening output file\n");
        goto end;
    }

    avio_open(&output_format_context->pb, out_filename, AVIO_FLAG_WRITE) ფუნქციით იქმნება აუტპუტ ფაილი

    avformat_write_header(output_format_context, NULL) ფუნქციით იწერება ვიდეოს ჰიდერი
```
    




