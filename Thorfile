# encoding: utf-8

require 'zip/zipfilesystem'
require 'csv'
require 'tk'


class Editor
  
  DEFAULT_WIDTH  = 600
  DEFAULT_HEIGHT = 400
  
  COL = [ '1 Text', '1 Image', '2 Text', '2 Image' ]
  
  SPECIAL_CHARS = "∑∏αβ≤≠≥⇒→∈∉∩∪∅∫"
  
  def initialize(name)
    @name        = name
    @filename    = "#{@name}.csv"
    @csv         = CSV.read(@filename, headers: true)
    @current_row = 0
    
    @csv << [] if @csv.count == 0
    
    @window = setup_window
    display_row
    update_title
  end
  
  
  def center
    # TODO: How do I do this?
  end
  
  def focus
    # TODO: How do I do this?
    @window.focus
  end
  
  def focus_front_image
    @front_text.focus(true)
  end
  
  def first!
    save_row
    @current_row = 0
    display_row
    update_title
    focus_front_image
  end
  
  def previous!
    save_row
    @current_row -= 1
    @current_row = 0 if @current_row < 0
    display_row
    update_title
    focus_front_image
  end
  
  def next!
    save_row
    @current_row += 1
    @csv << [] if @current_row >= @csv.count
    display_row
    update_title
    focus_front_image
  end
  
  def last!
    save_row
    @current_row = @csv.count - 1
    display_row
    update_title
    focus_front_image
  end
  
  def delete!
    @csv.delete(@current_row)
    @current_row = @csv.count - 1 if @current_row >= @csv.count
    # save_row
    display_row
    update_title
    focus_front_image
  end
  
  def insert!
    # save_row
    @csv << @csv[@csv.count-1].clone
    # p @csv[@csv.count-1]
    
    ((@current_row+1)...(@csv.count-1)).reverse_each do |index|
      # puts "Copying #{index-1} to #{index}"
      # p @csv[index-1]
      # p @csv[index]
      @csv[index] = @csv[index-1].clone
    end

    # row = @csv[@current_row].clone
    # row[COL[0]] = ''
    # row[COL[1]] = ''
    # row[COL[2]] = nil
    # row[COL[3]] = nil
    # @csv[@current_row] = row
    @csv[@current_row] = CSV::Row.new(COL, [nil] * COL.count)
        
    display_row
    save_row
    update_title
    focus_front_image
  end
  
  def save!
    save_row
  end

  private
  
  def update_title
    @window.title = "#{@name} #{@current_row+1}/#{@csv.count}"
  end

  def display_row
    row = @csv[@current_row]
    @front_text.clear
    @back_text.clear
    @front_text.insert(1.0, row[COL[0]])
    @back_text.insert(1.0, row[COL[2]])
    @front_image.textvariable.value = row[COL[1]]
    @back_image.textvariable.value = row[COL[3]]
  end
  
  def save_row
    row = @csv[@current_row].clone
    row[COL[0]] = @front_text.get(1.0, 'end-1c')
    row[COL[2]] = @back_text.get(1.0, 'end-1c')
    row[COL[1]] = @front_image.textvariable.value
    row[COL[3]] = @back_image.textvariable.value
    row[COL[1]] = nil if row[COL[1]].empty?
    row[COL[3]] = nil if row[COL[3]].empty?
    @csv[@current_row] = row
    File.open(@filename, 'w') do |file|
      file.write @csv.to_csv
    end
  end
  
  def setup_window
    root = TkRoot.new
    container = TkFrame.new(root) {
      padx 10
      pady 10
    }
    
    
    text_height = 6
    @front_text = TkText.new(container, borderwidth: 1, height: text_height).grid(sticky: 'news', column: 0, row: 10, columnspan: 12)
    @front_image = TkEntry.new(container).grid(sticky: 'news', column: 0, row: 15, columnspan: 12)
    
    @back_text  = TkText.new(container, height: text_height).grid(sticky: 'news', column: 0, row: 16, columnspan: 12)
    @back_image = TkEntry.new(container).grid(sticky: 'news', column: 0, row: 19, columnspan: 12)
    
    @front_image.textvariable = TkVariable.new
    @back_image.textvariable = TkVariable.new
    
    @last_focus = @front_text
    @front_text.bind('FocusIn') {
      @last_focus = @front_text
    }
    @back_text.bind('FocusIn') {
      @last_focus = @back_text
    }
    

    editor = self
    column = 0
    row = 0
    SPECIAL_CHARS.each_char do |character|
      char_button = TkButton.new(container, text: character, takefocus: false).grid(sticky: 'news', column: column, row: row)
      column += 1
      if column > 11
        column = 0
        row += 1
      end
      char_button.command {
        @last_focus.insert('end-1c', character)
      }
    end
    
    
    first_button = TkButton.new(container, text: "<<", takefocus: false).grid(sticky: 'news', column: 0, row: 30, columnspan: 1)
    prev_button = TkButton.new(container, text: "<", takefocus: false).grid(sticky: 'news', column: 1, row: 30, columnspan: 1)
    next_button = TkButton.new(container, text: ">", takefocus: false).grid(sticky: 'news', column: 10, row: 30, columnspan: 1)
    last_button  = TkButton.new(container, text: ">>", takefocus: false).grid(sticky: 'news', column: 11, row: 30, columnspan: 1)
    delete_button = TkButton.new(container, text: "Delete", takefocus: false).grid(sticky: 'news', column: 3, row: 30, columnspan: 3)
    insert_button = TkButton.new(container, text: "Insert", takefocus: false).grid(sticky: 'news', column: 6, row: 30, columnspan: 3)

    first_button.command { editor.first! }
    prev_button.command { editor.previous! }
    next_button.command { editor.next! }
    last_button.command { editor.last! }
    delete_button.command { editor.delete! }
    insert_button.command { editor.insert! }
    

    container.pack(fill: 'both')
    # container.grid(sticky: 'news', columnspan: 12, rowspan: 12)
    container.focus
    
    root.bind('Control-p') { editor.previous! }
    root.bind('Control-n') { editor.next! }
    root.bind('Control-s') { editor.save! }
    root.protocol("WM_DELETE_WINDOW") { editor.save!; root.destroy }
    # root.bind('Destroy') { editor.save! }
    
    root
  end
  
  
end


class MentalLess < Thor
  
  EXTENSION = '.studyarch'
  
  STATUS_INFO  = '[INFO]'
  COLOR_INFO   = :clear
  STATUS_ERROR = '[ERROR]'
  COLOR_ERROR  = :red
  
  no_tasks do
    
    def make_archive(name)
      Zip::ZipFile.open("#{name}#{EXTENSION}", Zip::ZipFile::CREATE) do |zip|
        zip.dir.mkdir('Archive')
        zip.dir.mkdir('Archive/Groups')
        zip.dir.mkdir("Archive/Groups/#{name}")
        zip.dir.chdir("Archive/Groups/#{name}")
        yield zip
      end
    end
    
    def copy_data(archive, file)
      basename = File.basename(file, File.extname(file))
      archive.dir.mkdir(basename)
      archive.dir.chdir(basename)
      
      # copy csv
      archive.file.open('Data.csv', 'w') do |csvfile|
        csvfile.write(File.read(file))
      end
      
      # copy necessary images
      images = []
      csv = CSV.read(file, headers: true)
      csv.each do |row|
        ['1 Image', '2 Image'].each do |header|
          images << row[header] unless row[header].nil? or row[header].empty?
        end
      end
      images.each do |image|
        say_status STATUS_INFO, "Copying '#{image}'.", COLOR_INFO && !options.silent?
        archive.file.open(image, 'wb') do |imagefile|
          imagefile.write(File.read(image))
        end
      end
      
      archive.dir.chdir('..')
    end
    
  end
  
  option 'silent', aliases: '-s', type: :boolean, default: false,
    desc: "Don't print status information"
  
  desc "convert FILE [FILE, ...]", "convert all the FILEs to a study archive"
  method_option :name, aliases: '-n', type: :string, default: nil,
    desc: "Name the archive NAME"
  method_option :force, aliases: '-f', type: :boolean, default: false,
    desc: "Overwrite existing files without asking"
  def convert(*files)
    if files.empty?
      say_status STATUS_ERROR, "No files given.", COLOR_ERROR
      exit
    end
    p options.name
    name = options.name || File.basename(File.absolute_path('.'))
    archive_name = "#{name}.studyarch"
    
    if File.exists?(archive_name)
      if options.force? or yes? "Archive '#{archive_name}' already exists. Overwrite it? [yes|no]"
        File.unlink(archive_name)
      else
        say_status STATUS_ERROR, "Aborting.", COLOR_ERROR
        exit
      end
    end
    
    say_status STATUS_INFO, "Creating archive '#{archive_name}'.", COLOR_INFO && !options.silent?
    make_archive(name) do |archive|
      files.each do |file|
        say_status STATUS_INFO, "Adding '#{file}'.", COLOR_INFO && !options.silent?
        copy_data(archive, file)
      end
    end
  end
  
  desc "create NAME", "creates a new card stack named NAME"
  def create(name)
    filename = "#{name}.csv"
    if File.exists?(filename)
      say_status STATUS_ERROR, "File '#{filename}' already exists! Aborting.", COLOR_ERROR
      exit
    end
    
    CSV.open(filename, 'w') do |csv|
      csv << ['1 Text', '1 Image', '2 Text', '2 Image']
    end
  end
  
  desc "edit NAME", "edits the card stack NAME"
  def edit(name)
    filename = "#{name}.csv"
    unless File.exists?(filename)
      if yes? "Stack '#{name}' does not exist. Do you want to create it? [yes|no]"
        invoke :create, name
      else
        say_status STATUS_ERROR, "Aborting.", COLOR_ERROR
        exit
      end
    end
    
    editor = Editor.new(name)
    editor.center
    editor.focus
    Tk.mainloop
  end
    
end
