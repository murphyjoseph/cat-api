require "rake"
require "rake/clean"
require "rdoc/task"

require "./app"

task :pre_fetch_cats do
  include CatRoamer

  last_stored_page = $redis.get('cats:last_stored_page')&.to_i || 0
  puts "last_stored_page: #{last_stored_page}"
  next_pages = [(last_stored_page+5), 2010].min

  puts "fetching and storing cats from #{last_stored_page} to #{next_pages}"
  (last_stored_page..next_pages).to_a.each do |page|
    html = Nokogiri::HTML(open("http://d.hatena.ne.jp/fubirai/?of=#{page}"))
    cat_urls = html.css("img.hatena-fotolife").to_a.map{|child| child.attributes["src"].value }

    puts "storing: #{cat_urls.count} urls from page: #{page}"
    store_cat_urls(cat_urls)

    cat_urls.each do |url|
      save_image_to_redis(url)
    end

    $redis.set('cats:last_stored_page', page)
    sleep(30)
  end


end