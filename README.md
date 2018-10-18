package PubSubReceiver;

import java.io.IOException;
import java.lang.System;


import com.google.api.client.json.jackson2.JacksonFactory;
import com.google.api.client.util.Key;
import java.io.FileOutputStream;
import java.io.File;
import javax.xml.bind.DatatypeConverter;
import java.io.InputStream;
import java.util.Scanner;
import java.nio.charset.StandardCharsets;

public class Parser {

    public static String readParam(InputStream i) throws IOException {
        String text = null;
        try (Scanner scanner = new Scanner(i, StandardCharsets.UTF_8.name())) {
            text = scanner.useDelimiter("\\A").next();
        }
        return text;
    }

    public static void main(String[] args) throws IOException {
        String in = "{\"number\":1,\"files\":[{\"name\":\"resume.txt\",\"contentType\":\"text/plain\",\"base64Content\":\"SGlyZSBtZSE=\"}]}";

        PubSubAttachments psAtt = JacksonFactory.getDefaultInstance().fromString(in, PubSubAttachments.class);

        for(int i = 0; i < psAtt.files.length; i++)
        {
            System.out.println("file #" + 1);
            String fileName = psAtt.files[i].name;
            String fileContentType = psAtt.files[i].contentType;
            String fileBase64Content = psAtt.files[i].base64Content;


            byte[] blob = DatatypeConverter.parseBase64Binary(fileBase64Content);

            System.out.println(blob.length);
            File file = new File("src/main/" + fileName);
            FileOutputStream out = new FileOutputStream( file );
            out.write( blob );
            out.close();

        }

    }

    public static class PubSubAttachments {
        @Key("number")
        private int number;
        @Key("files")
        private PubSubFile[] files;
    }

    public static class PubSubFile {
        @Key("name")
        private String name;
        @Key("contentType")
        private String contentType;
        @Key("base64Content")
        private String base64Content;
    }

}


//            InputStream inputStreamFile = new ByteArrayInputStream(bytesFile);
//
//            System.out.println(readParam(inputStreamFile));
//
//            //FileOutputStream stream = new FileOutputStream("/Users/ebarantseva/Desktop/java/Test/Work/src/" + fileName);
//            File targetFile = new File("/Users/ebarantseva/Desktop/java/Test/Work/src/" + fileName);
//
//            FileUtils.copyInputStreamToFile(inputStreamFile, targetFile);
