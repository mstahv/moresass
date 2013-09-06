Example project using original SASS compiler with Vaadin
========================================================

This is an example project that uses the original Ruby based sass compiler with Vaadin themes, instead of the one provided by Vaadin. This might be best option for advanced sass hackers or those who find issues with the Vaadin's built in theme/sass compiler. Also it is necessary for those you'd like to use advanced sass tools like [Compass](http://compass-style.org).

The solutions is based on sass-maven-plugin. The setup is probably not quite perfect, but it was more like my POC on the subject. Before applying this solution, you should also check alternative tools like [wro4j](https://code.google.com/p/wro4j/) (including support for e.g. [LESS](http://lesscss.org) ).

If you have suggestion to improve the example, feel free to fork.

Instructions to use the real sass compiler in Vaadin app
--------------------------------------------------------

1. Create new project with vaadin-archetype-application
2. Create directory themes under src/main
3. Extract contents of "/VAADIN/themes" from vaadin-themes jar contents to src/main/themes. This is necessary as at least with default settings sass plugin dont look for sass sources from classpath (and you also most probably want to remove vaadin-themes from your classpath). Also translate all relative image path to such that are understood by the original sass compiler. To simplify this step, you can copy those theme files from this project or use e.g. tool by [Alex Weirig](https://vaadin.com/forum/#!/thread/3167554)
4. Move the "mytheme" theme stubb created by archetype from /src/main/webapp/VAADIN/themes/mytheme to this new themes directory.

5. Configure sass-mave-plugin to compile your theme only:``
  <plugin>
    <groupId>org.jasig.maven</groupId>
    <artifactId>sass-maven-plugin</artifactId>
    <configuration>
    	<resources>
    		<resource>
    			<source>
    				<directory>${basedir}/src/main/themes/mytheme</directory>
    			</source>
    			<destination>${basedir}/src/main/webapp/VAADIN/themes/mytheme</destination>
    		</resource>
    	</resources>
    </configuration>
    <executions>
    	<execution>
    		<goals>
    			<goal>update-stylesheets</goal>
    		</goals>
    		<phase>process-resources</phase>
    	</execution>
    </executions>
  </plugin>`` 
  
  
  If you are extremely pedant with maven conventions, compile to output directory, but then notice that e.g. jetty:run or WTP servers don't find your compiled css without further configuration.

Now sass compilation is hooked to your normal maven build. Also you can force it manually with "sass:update-stylesheets" maven target. During the development you can also use "sass:watch" which detects changes automatically and immediately compiles your theme stuff when you modify your sass files.

You can still optimise the setup slightly by ignoring vaadin-theme-compiler module and its transient dependencies. At least it should bring down your war file size. Add following exclusion rule to your vaadin-server dependency:

``  <exclusions>
  	<exclusion>
  		<groupId>com.vaadin</groupId>
  		<artifactId>vaadin-theme-compiler</artifactId>
  	</exclusion>
  </exclusions>
``

Also you can remove execution hooks for "theme-update" and "theme-compile" from vaadin plugin configuration. One could also thing that vaadin-themes is now obsolete, but it is needed for some default theme images. If you use e.g. Compass project to make your sprites, you can probably drop it totally. 

